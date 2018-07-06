Title: Automating File Renaming
Date: 2018-06-19 01:09
Modified: 2018-06-19 01:09
Category: Python
Tags: pelican, publishing
Slug: automating-file-renaming
Authors: Cameron Yee
Summary: Learn how to use Python to enforce file naming conventions.


## What:

Today I am going to explain how to use Python and a few other tools to automatically rename downloaded files to match wanted naming conventions.

## Why:

We've all visited websites that contain spaces in the urls.  While functionality is not affected, seeing '%' signs in a url is ugly and unprofessional.  Because of this, it is a good idea for companies to implement naming conventions for files that will be opened on a web browser.

## Who:

This post will be useful to anyone that works with file organization.  At [BSCS](https://bscs.org/), a lot of our projects involve websites that link to files that open in browser tabs.  We store all of our project files on a media server.  In order to keep the server as organized and clean as possible, all files that are stored on it must be named properly before being added.  More often than not, documents are not named properly when they are sent to our team to store on the media server.  Because our team was consistently renaming files, I decided to automate the process to save us time on this tedioustask. 

## How:

This process will involve 3 steps: Python, fswatch installation, and Configuration.

### Step 1. Python

I used Python to change the file names.  Create a new file called **rename_files.py**. The basic Python code is as follows:

**1\.** Set a directory path where you want to rename files.  Most likely **/Users/&lt;username&gt;/Downloads**.

**2\.** Get every file path in the directory, and ignore files that you want to ignore.

```python
def getFilePaths(directory):
    files = []
    #Gets all files in given directory
    dir_files = os.listdir(directory)
    #Creates new list of files in the directory while ignoring files where  we don't want to change the name
    relevant_files = [f for f in dir_files if f not in ['.DS_Store', '.localized']]

    #Appends the file name to the directory path to get a full file path for each file
    for f in relevant_files:
        files.append(directory + '/{}'.format(f))

    return files
```

**3\.** Change the file names to match naming conventions. (This will depend on what you want).

For us, we want to all our file names to be lower case, have no unnecessary punctuation, and have underscores in between words:

```python
def renameFile(f):
    #Gets file name from path
    base = os.path.basename(f)
    #Ensures file name does not contain capital letters
    base_lower = base.lower()
    #Replaces spaces with underscores
    name = base_lower.replace(' ','_') 
    #Calls function to remove punctuation seen below, stores value in new variable so we're not mutating variables
    name_2 = stripPunctuation(name)

    #Calls function that is explained below
    final_name = removeDoubleFileType(name_2)

    #Gets path of directory that file is stored in ('/Users/<username>/Downloads')
    dirname = os.path.dirname(f)
    #Ensures that our original file path is type String.  This is important for os.rename()
    sf = str(f)
    #Stores our new file path with the new name
    sdn = str(dirname + '/' + final_name)
    #Renames the original file with the new name
    os.rename(sf,sdn)
```

There are two function calls above.  The first removes punctuation from the file name:

```python
def stripPunctuation(name):
    #Removes underscores from the list of puncuation to check for
    x = string.punctuation.replace('_','')
    #Removes periods from the list of puncuation to check for
    punct = x.replace('.','')
    #Makes a punctuation table that is useable by string.translate()
    table = str.maketrans('', '', punct)
    #Actually removes punctuation from str
    final_name = name.translate(table)
    return final_name
```

  In our Google Drive, some of the files are named '&lt;name&gt;.docx', so they would be named '&lt;name&gt;.docx.pdf' if we download them as a PDF. The second function makes sure that this is fixed in the file rename:

```python
def removeDoubleFileType(name_2):
    final_name = re.sub('docx.pdf$','pdf', name_2)   
    return final_name
```

 Finally, we call the appropriate functions.  Our final main function looks like:

```python
if __name__ == '__main__':
    #Hardcoded to automate with fswatch
    directory = '/Users/<user>/Downloads'
    files = getFilePaths(directory)
    for f in files:
        renameFile(f)
```

### Step 2. Install fswatch

At this point, we can rename files in a folder by running `python3 rename_files.py`.  Now we are going to automate this so that we do not have to run a python script manually every time we want to download a file.  For this, we are going to use a file change monitor called fswatch.  To use fswatch, follow the installation instructions from one of these sites:

<http://emcrisostomo.github.io/fswatch/doc/>

<https://github.com/emcrisostomo/fswatch>

I recommend using Homebrew and running the following command from Terminal:

```bash
$ brew install fswatch
```

Once fswatch is installed it should come ready to use.  If not, see the fswatch documentation for help.

### Step 3. Configuration

The final step is to turn on fswatch to watch the Downloads folder, and run rename_files.py if anything is downloaded.  First, reopen **rename_files.py** and add the following to the top of the file:

```python
#!/usr/local/bin/python3
import sys
import os
from glob import glob
import string
import re
```

This will allow the file to be called from bash.  Next, we must give the system permission to execute the python file.  In terminal enter:

```bash
chmod u+x <path>/rename_files.py
```

Finally, we must call fswatch.  In the home directory (**/Users/&lt;user&gt;/~**), edit the file called **.bash_profile**.  Create a new file with the same name if it does not yet exist.  The file needs to contain at least these two lines:

```bash
export PATH="$PATH:/Users/<user>/bin"
fswatch -om fsevents_monitor ~/Downloads | xargs -n1 <path>/rename_files.py &
```

The **export** line sets the location that the system looks for when a command is ran in command line.  The fswatch line monitors the Downloads folder and executes rename_files.py if an event is heard.  We are using fswatch whenever **.bash_profile** is read.  Therefore, for this automation to work, a command line with bash must be running.

That's it!  You can test to see if it works by opening command line, and downloading a file.  Check in the Downloads folder to see if the file is named correctly.  Another check is to use the `jobs` command in command line.  If fswatch is running, `jobs` will show it.

## Duly Noted:

This solution works, but there are more efficient solutions.  It is possible to run fswatch without command line running by writing an OS daemon that runs on startup.  This is a better solution because it is not reliant on another program.  Furthermore, there is an issue with running fswatch through Terminal as a background process because multiple instances of fswatch will run if multiple terminal windows are open.  While this is an issue, I do not think it is a big enough deal not to use my solution.  As a tech person, I am constantly using Bash so I do not care that Bash has to be running.  Lastly, this process could be done purely in Bash and eliminate the need for a python script.  Let me know of ways this process can be further improved.

# View code:

<https://github.com/cameron-yee/rename_files>
