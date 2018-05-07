---
layout: post
cover: false
title: Retrieving HTML from parsed XML in PHP
date:   2016-07-25 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## Objective:
We want to take in nested HTML within an XML file and parse out tags and content within the tags to do "something" with.

~~~
require "../vendor/autoload.php";
use PHPHtmlParser\Dom;

    function recurseXML($xml,$parent="") {
       $child_count = 0;
       foreach($xml as $key=>$value) {
          $child_count++;
          if($this->recurseXML($value,$parent.".".$key) == 0) {
             print($parent . "." . (string)$key . " = " . (string)$value . "<BR>\n");
          }
       }
       return $child_count;
    }

    function getNode($xml) {
        $output = array();
        $i = 0;
        $result = $xml->xpath('/go/to/a/path');
        while(list( , $node) = each($result)) {
            $output[$i] = (string)$node;
            $i++;
        }
        return $output;
    }

    function parseXml() {
        $xmlFilename = '/your/filename.xml';
        $xml = simplexml_load_file($xmlFilename);
		$node = getNode($xml);

    	$num = 0; # this is the selector of the nested html tag

        foreach ($node as $key => $n) {
            $dom = new Dom;
            $n = html_entity_decode($node[$key], ENT_COMPAT);
            $dom->loadStr($n, []);
            $test = $dom->outerHtml;
            $contents = $dom->find('html_tag_name')[$num]; # html_tag_name can be anything like 'table'

            foreach ($contents as $content) {
                # do something with the html
                $html = $content->innerHtml;
                $output[$key]['name_this_whatever'] = $html; # the name can be anything you want as your key
            }
        }

        $file = fopen('../where/you/want/to/write.csv', 'w');
        fputcsv($file, array('name_this_whatever'));

        foreach ($output as $out) {
            fputcsv($file, $out);
        }

        fclose($file);
    }

    parseXml();
~~~
{: .language-php}
