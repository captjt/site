---
layout: post
cover: false
title: Recursively Combine .csv files in PHP
date:   2016-07-25 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## Overview of the Problem
Recently I have been trying to figure out a way to run a *PHP* script that will take two .csv files full of data and determine where they are the same and combine the two into a new .csv to then import my data as one file.

The main reasoning behind the overall problem is that I have two sources of data that I am ingesting. Each source of data has one similar data field throughout so it can be thought of as a 'foreign key' type of deal. With all of that being said I wanted to start with a small tutorial of how I broken the problem down and the code implementing the process!

### Implementing the recursive merge
First we need to decide how to attack our two arrays. I thought about it and being the person that loves to try and recursively do things to keep my code cleaner. I decided to implement this small function that took another rendition of the [array_merge_recursive](http://php.net/manual/en/function.array-merge-recursive.php) function that PHP implements that I believe is more than I think this application needs.

If you look at the comments I made in the code it helps explain the actions much better.

~~~
/**
 * merge_array function to take two array and recursively check in there are keys in two arrays that are the same
 *    this will output an array that will only contain the combined array data
 * @param  array $array1 first array of data
 * @param  array $array2 second array of data
 * @return array         returns array of data combined from the two inputted arrays
 */
function combine_arrays(array & $array1, array & $array2) {
    $temp = $array1; # set our temporary array

    # loop through the second array
    foreach ($array2 as $key => & $value) {
        # check if the value in position $key of the second array is an array type
        #   and if the temporary array we set $temp at the same $key is not null
        #   and if the value of $temp is an array
        if (is_array($value) && isset($temp[$key]) && is_array($temp[$key])) {
            # set the temporary array at $key to be a recursive call to combine_arrays
            $temp[$key] = combine_arrays($temp[$key], $value);
        }
        # check if the $key is a number
        elseif (is_numeric($key)) {
            # check if value in $array2[$key] is not in the temporary array ($temp)
            if (!in_array($value, $temp))
                # if the value is not in the array then set the value in $temp
                $temp[] = $value;
        }
        else
            # else set $temp[$key] to the $value from $array2[$key]
            $temp[$key] = $value;
    }

    # return our temporary array
    return $temp;
}
~~~
{: .language-php}

After looking at the code the function will return a combined array that checked for same values from one array to another. This is a way to accomplish the final outcome because sometimes you would want to create a script just to create a prepared statement to insert and then another to update the data on that alike column between the csv files.

Anyways onto the next step we need to retrieve our data!

### Retrieving .csv data for our arrays

Here a simple template function for retrieving .csv data from files you have locally.

~~~
/**
 * get_csv function to take in a filename (String) and a delimiter (String) and output an array value for the file location
 * @param  string $filename file location to fetch the data from
 * @param  string $delim    changes the delimiter that the csv is formatted in
 * @return array            this will return an array of string data that the read csv contains
 */
function get_csv($filename, $delim = ","){

    $row = 0;
    $dump = array();

    $f = fopen ($filename,"r");
    $size = filesize($filename)+1;
    while ($data = fgetcsv($f, $size, $delim)) {
        $data = str_replace(array("\n", "\t", "\r"), '', $data);
        $data = str_replace("'", "''", $data);
        $dump[$row] = $data;
        $row++;
    }
    fclose ($f);

    return $dump;
}
~~~
{: .language-php}

This function will get a specific file that you have passed in and you can manipulate the *delimiter* depending on how your .csv is formatted. This will fetch each 'row' in the file and create a new node in the array that will be returned after it has gotten all the data.

This is all great - sure - but now what's the big thing that we should do to handle different types and make sure it's formatted correctly for our database importing.

I am using *PostgreSQL* for this application and I know from experience that you must use double single quotes to escape single quotes and other small things that are needed for my data set that I am importing. You can use a simple *PHP* function called `str_replace()` to replace characters with other specific characters you want.

**BUT...**

We need to get our data still.. Two simple calls - here we go

~~~
# get the two csvs into arrays
$array1 = get_csv("/your/file/location");
$array2 = get_csv("/your/file/location");
~~~
{: .language-php}

Now all we have to do is some small function calls with our two arrays we fetched. After we get back our combined array we will then write to a new file where we want our output to go.

~~~
# merge the two array's fetched from the csv's and outputs merged array to a new csv
$file = fopen('/write/to/this/location', 'w');
$finalArray = combine_arrays($array1, $array2);
foreach ($finalArray as $key => $value) {
    fputcsv($file, $finalArray[$key], ",", '"');
}
fclose($file);
~~~
{: .language-php}

With all that being said this is a very small and simple way to handle the problem that arises when we are trying to 'combine' or 'merge' .csv files that we are pulling into our applications. I know there are other ways to do it and I must say there probably is a better way to do this -- but here is a working explaination of my functionality.
