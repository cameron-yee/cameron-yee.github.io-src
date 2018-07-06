Title: Part 1: File Setup
Date: 2018-03-12 01:09
Modified: 2018-03-12 01:09
Category: Canvas
Tags: pelican, publishing
Slug: part-1-file-setup
Authors: Cameron Yee
Summary: Setup your file structure to make this work.


#Part 1: File Setup

I designed the Canvas LMS API CLI to work specifically with our file structure setup for our course html pages.  There is one important conventions to follow if you would like to use our code as is:

    1. HTML files should be named as: XX_page-url.html
        * Example: 00_welcome-page.html

These guidelines are based on how the CLI code is written.  The code can be easily altered to work with other file structure systems.  An example directory is shown below:

```
./
    top_directory/
        /sub_directory
            /sub_sub_directory
                00_first_file.html
                01_second_file.html
            /sub_sub_directory_1
                00_other_file.html
                01_next.html
        /sub_directory_1
            00_some_file.html
```


