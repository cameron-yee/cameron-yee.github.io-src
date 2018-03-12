Title: Part 1: File Setup
Date: 2018-03-12 01:09
Modified: 2018-03-12 01:09
Category: Python
Tags: pelican, publishing
Slug: part-1-file-setup
Authors: Cameron Yee
Summary: Setup your file structure to make this work.


#Part 1: File Setup

I designed the Canvas LMS API CLI to work specifically with our file structure setup for our course html pages.  There are two important conventions to follow if you would like to use our code as is:

    1. HTML files should be named as: XX_page-url.html
        * Example: 00_welcome-page.html
    2. If subdirectories are used, put EVERY HTML file in a subdirectory within the parent HTML directory.  It is also alright to have all of the HTML files stored in a single directory.  The important thing is to do one or the other.  DO NOT have HTML files in the parent directory with other HTML sub-folders.

These guidelines are based on how the CLI code is written.  The code can be easily altered to work with other file structure systems.

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

OR

```
./
    top_directory/
        00_first_file.html
        02_second_file.html
        03_other_file.html
```

In the first structure example, there are no HTML files directly in top_directory.  In the second structure example, there are no subdirectories because there are direct HTML files.

