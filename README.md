# Virtual Library

## Main Goal
- to virtualize all of my books, and apply a full text search
on the books' contents
- fine tune GPT-3 on the book contents to acheive a second-brain, where GPT
will be able to answer questions and summarize the books
- be able to access my books, fetch text, images or metadata from a webapp

## Procedure

- digitize all books, page by page
    - using hp printer and hplip suite, i can call `hp-scan [...options]` to
    initiate the scan from my laptop.
        - this process takes about 30s per page
        - `hp-scan` lives in `/usr/share/hplip/scan.py` and can potentially
        be modified to persist its connection to speed up scanning
    - pages dont all fit on the scan platform, so some pre-processing needs
    to occur, cropping, removing images, adjusting brightness for optimal OCR
- pages need to be organized by title, objects stored in s3/r2
- use OCR to extract text from scanned pages, insert text
into db with full-text index
- repeat for each book

after books are scanned and indexed, begin integrating OpenAI api and set up 
fine-tuning architecture

### Scanning
There exists a node script that takes a file name (book title) and sends the
scan-signal to the device. Scanned contents are then saved in a directory
based on the file name.
> this process takes about 30s per page, spend a few hours working on scan.py 
to optimize, otherwise this will take a while

### Processing
After the contents of a book has been completely scanned, we will need to go
thru every image, and rotate 90 degs left or right to acheive correct 
orientation. The images may also require cropping. 
- Build a small, local web app that facilitates the image processing.
    - should allow for marking images for left or right rotation, and apply
    the necessary crop
    - fully processed images should be saved to s3/r2, and a webhook can kick
    off db-ingress
- db-ingress process receives a message containing s3-object key, reads the
object and runs the OCR software (tesseract.js)
- the entire processing step will require a lot of manual intervention and 
QA

### Final Web App
Ultimately, I want to have a nice and simple UI to search and view my books.
The user should be able to select from categories 
(programming, music, cooking), search based on title, author, isbn, 
or contents.
After a book has been found, the user should be able to then access the book's
contents freely (maybe limited to outsiders, 3 books a day?).
Original images and figures should accompany the book text
