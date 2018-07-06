Title: Part 2: Canvas API Calls
Date: 2018-07-06 01:09
Modified: 2018-03-12 01:09
Category: Canvas
Tags: python, canvas
Slug: part-2-canvas-api-calls
Authors: Cameron Yee
Summary: Learn to interact with Canvas LMS API using python.


## What:

In this article, I will discuss how to use Python's requests library to make API calls to Canvas LMS.

## WHY:

Canvas' API allows us to alter courses, pages, modules, and more in a simple way.  I originally started using its functionality because I was tired of manually having to insert HTML pages into Canvas' HTML Editor every time a page had an update.  By using the API, I am now able to test page layouts almost instantaneously.  Additionally, if I need to make a change to every page in a course (ex. add a footer), I can update the entire course at once instead of manually updating every page.  It also can be used to query course or page data that is returned as a JSON object.

## WHO:

Anyone that is responsible for Canvas content management.

## HOW:

First, in a Canvas admin account go to **Account > Settings** on the left menu.  Scroll down to a section labeled **Approved Integrations**.  Generate a developer access token by clicking the '+ New Access Token' button.  This allows developers to test integrations with Canvas (such as API calls).  Make sure to save the generated token somewhere before leaving the page because it is unviewable afterwards.

Now for the code.  Make sure python's requests library is installed and imported.

```bash
$ pip3 install requests
```

In python file:

```python
import requests
```

Making requests is simple, but the code will vary slightly depending on the request.  Let's examine a request to update a wiki page's content:

```python
def updateIndividualPage(course_id, headers, page_url, file_path, html_content=None):
    url = url_base + course_id + /pages/' + page_url

    if html_content is not None:
        data = [('wiki_page[body]', html_content),]
        r = requests.put(url, headers=headers, data=data)
    else:
        with open(file_path, 'r') as f:
            html = f.read()
        data = [('wiki_page[body]', html),]
        r = requests.put(url, headers=headers, data=data)
        f.close


if __name__ == '__main__':
    access_token = getAccessToken() #Custom function
    headers = {"Authorization": "Bearer " + access_token}
    course_id = 1 #example course_id
    page_url = example-page-url
    file_path = '/Users/path-to-file/file.html'
    url_base = 'https://<organization>.instructure.com/api/v1/courses/'

    updateIndividualPage(course_id, headers, page_url, file_path)
```

Now let's go through this.

I created this function to be able to optionally take html content as a parameter in string format.  The url will consist of the required api url (`url_base` variable in main) with the path to the wiki page concatenated to the end.  This is the location where we want to send our data.  This function has 2 possible requests depending on if html content was provided in the function call.  The format is almost indentical either way.  For these requests, we are going to replace the existing content of an canvas wiki page with our new html content.  To do this, we will replace the wiki page parameter **wiki_page[body]**.  The possible options can be seen on the documentation site which I've linked to at the bottom of this post.  We will specify our replacement in the `data` variable by storing the Canvas parameter and the new content in a tuple.  To alter additional properties of the Canvas page, add additional tuples to the `data` variable:

```python
data = [('wiki_page[body]', html),
        ('wiki_page[title]', 'New Page Title'),]
```

The actual api request will take 3 parameters: url, headers, and data.

The url is the api base plus the location of the page we want to edit.  Headers provide the access_token so Canvas can authorize us making changes.  I would recommend storing the access token as an environment variable, or placing it in another python file as a variable and importing that file.  If the later option is chosen, make sure to add the file to .gitignore to avoid version tracking of the file.  The data parameter is the content that we want to update.  This specific request uses the PUT protocol to send the data in order to be saved.  Other requests use GET, POST, and DELETE depending on the goal.  These are specified in the Canvas API docs.

One last thing to note, is that the `else` statement reads and saves the html content to PUT if a file path is given, but no HTML content is provided.

## Duly Noted:

This system works well for testing API calls.  For creating programs for reuse (outside of individual use), Canvas requires that OAuth2 authentication be setup.  For reasonable usage, a system for calling the API requests should be set up.  Personally, I used python's argparse library to create a CLI (Command Line Interface) so I can update Canvas straight from terminal, giving the appropriate request paramters as command arguments.  Finally, python's requests library is not the only way to interact with the API.  Curl works well, and there are probably other possibilities as well.

#### [Canvas API Documentation](https://canvas.instructure.com/doc/api/index.html)



