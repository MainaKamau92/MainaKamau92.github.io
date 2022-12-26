---
title: "Working with PDFs in Python"
date: "2021-06-12"
---

Recently I tried out pdf manipulation in my code to extract data to use in a project. Being an unorthodox file system to use in data storage and manipulation as opposed to the more traditional excel and CSV files, it comes with some loops in python. In this walkthrough, I'll guide you on how to do exactly just that using a helper package called PyPDF2.

## Let's get started.

We'll begin by setting up our environment, create a folder followed by a virtual environment. You can choose `virtualenv` or `pipenv` for the virtual environment. I'll be using `pipenv`

Creating the virtualenv in the working directory:
```
mkdir pypdfdir
cd pypdfdir
pipenv shell
```
After creating the virtual environment you should proceed to install the package that will be central to our tutorial.

`pip install PyPDF2`

### Using PyPDF2 in Read Mode

Once the package has been installed we can begin. I have two pdf files in my root folder that I'll be using for demonstration. The first is a lorem ipsum dummy text data pdf file and the second has dummy data in a table structure.

Let's create a file where our code will reside, i'll call mine `pdf.py` . While in the directory run below command to create the file.

`touch pdf.py`

You'll start off by calling the `PDFReader` class that will give you read access to your pdf files. Add the code below to your python script.
```
import PyPDF2

def pdf_read_mode():
    reader = PyPDF2.PdfFileReader('lorem.pdf')
    # Get the number of pages in the pdf
    print(f"Number of pages are {reader.numPages}")

if __name__ == '__main__':
    pdf_read_mode()
```
You first import the package to gain access to its `PdfFileReader` class instance, you then proceed to pass in the pdf file name that is located on the same level as your script and call the method `numPages` which returns the number of pages our pdf has. You can now go to the terminal and run the file using the command

`python pdf.py`

Which should output the message indicating the integer value representing the number of pages in your pdf file:
```
Number of pages are 1
```

Let's test out more methods in the `PdfFileReader` class, add the below code to your python script, we'll maintain the `reader` instance:
```
import PyPDF2

def pdf_read_mode():
    file = open('lorem.pdf', 'rb')
    reader = PyPDF2.PdfFileReader(file)
    # # Get the number of pages in the pdf
    print(f"Number of pages are {reader.numPages}")
    # # Get metadata attached to a pdf file
    print(f"The pdf file has the following metadata: {reader.documentInfo}")
    
    # Get the page instance (we fetch the first page)
    page = reader.getPage(0)
    # We proceed to fetch the contents of the page by calling the extractText method
    print(page.extractText())

if __name__ == '__main__':
    pdf_read_mode()
```
In the code, you'll continue to extract the contents of a page. First, you'll call the page instance referencing the first page in your pdf file and then you'd call the `extractText` function which does as the name presumes, gets all the text on the page. You should see an output of the text in your terminal.

![](https://res.cloudinary.com/dae3oj71g/image/upload/v1672044167/GithubPages/pdfs_with_pythom/lorem-1024x528.png)


Above is the partial text outputted. You can proceed to explore some more functionality on your own.

### Using PyPDF2 in Write Mode

We'll move from reading pdf files for now and venture briefly to writing into pdf files. You'll be using the `PdfFileWriter` class of the package. Let's get started, add below code to your file
```
def pdf_write_mode():
    file = open('lorem.pdf', 'rb')
    reader = PyPDF2.PdfFileReader(file)
    page = reader.getPage(0)
    newpdf = open('new.pdf', 'wb')
    writer = PyPDF2.PdfFileWriter()
    writer.addPage(page)
    writer.write(newpdf)
    newpdf.close()

if __name__ == '__main__':
    # pdf_read_mode()
    pdf_write_mode()
```
### Write mode caveat...

Unfortunately, the package does not allow you to edit existing pdf files with new content from python.

It does, however, provide the ability to copy content from one pdf file to a new one. That's exactly what you'll do in the code above, going line by line firstly we open the old file in `read mode` and fetch the only page that exists. Secondly, proceed to create a new file by running the open file in `wb` (write mode) and providing an arbitrary name in this case `new.pdf` we proceed to create a new writer instance from the `PdfFileWriter` class and add the page instance defined earlier. Finally, use the writer class to write the new page content to our earlier created pdf file and close the file.

On a subsequent run of the script file, a new file originally 😉 labelled `new.pdf` should be created and it should have the same content as the earlier file.

## Merging two pdf files

We can also merge two or more pdf files into one, add the following code to your script.
```
def pdf_merger():
    file = open('lorem.pdf', 'rb')
    file2 = open('mock.pdf', 'rb')
    merger = PyPDF2.PdfFileMerger()
    final_pdf = open('final.pdf', 'wb')
    merger.merge(position=0, fileobj=file2)
    merger.merge(position=2, fileobj=file)
    merger.write(final\_pdf)
    final_pdf.close()

if __name__ == '__main__':
    # pdf_read_mode()
    # pdf_write_mode()
    pdf_merger()
```
In the code above we create a `pdf_merger` function and in it resides the code to merge the two pdf files we have. You can proceed to run the file again. A new pdf file is created and based on the position you provided while calling the merge instance here, `merger.merge(position=0, fileobj=file)` the file will be appended into that position/page.

## Conclusion

We'll pen the article at that, it's by no stretch of the imagination a comprehensive coverage of what the [PyPDF2](https://pythonhosted.org/PyPDF2/) python package can do and I would advise going through their documentation for more details. Check out more of my writing [here](https://mainakamau92.github.io/)

Happy programming.

Code used in the article can be found [here](https://github.com/MainaKamau92/pypdftut).
