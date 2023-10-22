---
layout: post
title:  "Automating Image Processing with Python"
author: jay
categories: [ code ]
tags: [ python, scans, automation ] 
image: assets/images/headers/python_pillow.png
description: "Automating Image Processing with Python"
featured: false
hidden: false
comments: false
redirect_from:
  - /post/2020/07/28
#rating: 4.5
---




<p>If you have been reading this blog, you'll know I collect sports cards. It's fun to share what I have with other collectors (by posting scans on sportscollectors.net, facebook, etc.).</p>
<p>A few years ago I bought a <a title="Brother MFC-9130CW printer" href="https://www.amazon.com/gp/product/B00C6MNP52/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=B00C6MNP52&amp;linkCode=as2&amp;tag=hipstir-20&amp;linkId=53bf0932815fceb1355ecfab28f5c264" target="_blank">Brother MFC-9130CW</a> all in one printer/scanner that I use to do my scanning. I usually set it to scan documents in legal format so I can fit 9 cards on a scan (3 rows by 3 columns). And since I want to do this most efficiently, I generally save them as a 200dpi pdf file with multiple pages.</p>
<p><strong>The requirements:</strong></p>
<ol>
<li>We will have a multipage pdf, with each page containing a 3x3 grid of equal sized card images. I will use this sample file for this exercise:&nbsp;<a href="/FILES%2f2020%2f07%2fsample_cards_file.pdf.axdx" target="_blank">sample_cards_file.pdf (3.02 mb)<br /><br /></a></li>
<li>We will need each image cropped to show the full card (we can have some extra space around it) and saved to it's own jpeg file. An example is show below:<br /><br />

<p><img src="{{ site.baseurl }}/assets/images/1989_hoops_english.jpg" alt="" /></p>

<br />&nbsp;</li>
<li>I will supply a directory path containing the outputted images. Since I will put them in my inventory database, I'll need to supply the starting number for the image names and have each subsequent image increment by one.<br /><br />

<p><img src="{{ site.baseurl }}/assets/images/1989_hoops.png" alt="" /></p></li>
</ol>
<p><strong>The process:</strong>&nbsp;</p>
<p>I like using Python for automating things, as it seems to have libraries for most things I want to do.&nbsp; So I figured it would be a good candidate for this project.</p>
<ol>
<li>The first thing we will want to be able to define some global variables<br /><br />
<pre class="brush: py;">#paths
source_file = '/tmp/sample_cards_file.pdf'
out_dir = '/tmp/process/'

# page setup
rows = 3
cols = 3

# defines where the last run of this left off on (first item will be 1.jpg if 0)
starting_count = 0

#spacing offsets
top_offest = 0
left_offset = 260
bottom_offset = 0
right_offset = 0
spacer = 40
vert_spacer=0</pre>
</li>
<li>We will need to do is convert the each page of the pdf to a jpeg file.<br /><br />Python has a library called pdf2image to do this:<br /><br />
<pre class="brush: py;">pip install pdf2image
pip install poppler

# I used this syntax instead do of pip when running this in a jupyter notebook
# conda install -c conda-forge pdf2image
# conda install -c conda-forge poppler

from pdf2image import convert_from_path

# function that converts multipage pdf to individual
# jpeg images. Function returns list of image paths.
def convert_pdf_to_jpegs(pdf_path, out_dir):
    file_paths=[]
    pages = convert_from_path(pdf_path, 500)
    page_count = 1
    for page in pages:
        image_path="{}temp_page_{}.jpg".format(out_dir, str(page_count))
        #add to the list
        file_paths.append(image_path)
        #save converted file
        page.save(image_path, 'JPEG')
        page_count += 1
    return file_paths</pre>
</li>
<li>We will have a function that breaks up an image with our 9 cards and save each individually to a specified directory. We will need the ability to seed the first image name. The function can use the spacing offset variables<br /><br />
<pre class="brush: py;">from PIL import Image

def split_images_from_page(image_path, out_dir, row_count, col_count, start_number):
    # opens the image file 
    Im = Image.open(image_path)

    # calculates height and width of image
    full_width = Im.width
    full_height = Im.height
    image_height = int((full_height - vert_spacer - top_offest - bottom_offset) / rows)
    image_width = int((full_width - spacer - left_offset - right_offset) / cols)
    
    image_count = 0
    row_current=1

    #iterates through rows and columns
    while row_current &lt;= row_count:
        col_current = 1
        while col_current &lt;= col_count:

            # calculates the coordinates on the image to crop            
            croppedIm = Im.crop((left_offset + ((col_current - 1) * image_width) + spacer, top_offest + ((row_current - 1) * image_height), min(left_offset + (col_current * image_width) + ((col_current - 1) * spacer), Im.width), top_offest + (row_current * image_height) + vert_spacer))

            # if you wanted to resize the image to 300 width and 420 height 
            # croppedIm = croppedIm.resize((300, 420))
            
            # saves the image to specified directory
            croppedIm.save("{}/{}.jpg".format(out_dir, start_number+image_count))
       
            col_current += 1
            image_count += 1        
        row_current+=1</pre>
</li>
<li>Calling the functions:<br /><br />
<pre class="brush: py;">import os

page_count = 1

# convert pdf to jpeg file for each page
file_paths = convert_pdf_to_jpegs(source_file, out_dir)

# split each page into images
for file_path in file_paths:
    number_start = starting_count + (page_count-1) * (rows * cols) + 1 
    split_images_from_page(file_path, out_dir, rows, cols, number_start)
    page_count += 1

# clean up delete jpeg files for each page
for file_path in file_paths:
    os.remove(file_path)</pre>
</li>
</ol>
<p>&nbsp;</p>
<p>Here is my ipython notebook with the code detailed above:&nbsp;</p>
<p><a href="{{ site.baseurl }}/assets/files/pdf_scan_process.ipynb" target="_blank">pdf_scan_process.ipynb</a></p>