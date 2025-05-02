---
title: "A Live PDF Coordinate Viewer"
description: "For Easier PDF Editing"
date: "2024-08-08"
draft: false
author: "Kean Teng Blog"
tags: ["Python", "PDF", "Coordinate"]
weight: 5
summary: "It is indeed frustrating to not know the location when you want to visit a certain place. Likewise, it is irritating to not know the PDF coordinate when you rerun your code a couple of times just to get the text fill in the correct location"
---

<center><img src=https://images.unsplash.com/photo-1642013352168-ccb46a0ac67f?q=80&w=1771&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Image from Unsplash</i></p>

It is indeed frustrating to not know the location when you want to visit a certain place. Likewise, it is irritating to not know the PDF coordinate when you rerun your code a couple of times just to get the text fill in the correct location of a PDF form or even to get an image to situate properly in your invoice. 

That's exactly how I feel when I was using the tool [PDF-Lib](https://pdf-lib.js.org/) to create an invoice generating app because I have to rerun the code a few times just to get some texts land properly on the location I want on a PDF document. 

I try to look up information online, I search for "PDF coordinate viewer", "how to get the coordinate in a PDF docuement" and many more and the solutions either ask me to install a software or even purchase one just simply to view the PDF coordinate. I am surprise that there's no website cater for this feature in 2024! Regardless, I have to work out the solution myself.

To solve this problem we have to understand that the PDF coordinate system works a bit differently. For example, let's say an image, the origin for this image if we think of placing it on a cartesian plane is at the top-left corner. But, for PDF, it is at the bottom-left corner. This information is crucial as we need to perform conversion to get the correct PDF coordinate, we cannot simply use our cursor coordinate on screen.

Here's how we can do the conversion:

```
pdf_x = (canvas_x / canvas_width) * page_width
pdf_y = (canvas_height - canvas_y) / canvas_height * page_height
```

Basically, the formula will normalizes the coordinates on a canvas, let's say your cursor coordinate on screen to represent the fractional position along the canvas height or width. Of course, the formula will also take care of the difference of origin between the canvas and PDF document. Then, we will scale these values to the actual height of the PDF.

## Python Program

With this formula, we can create a Python program to allow use to track our cursor on screen, the screen will need to show the PDF document we want, and then as we click around the screen it will display the converted cursor coordinate in the form of PDF coordinate:

![Alt Text](https://github.com/keanteng/live-pdf-coordinate/blob/main/demo.gif?raw=true)

We will make use of the `PyMuPDF` library to allow us to work with PDF document, you need to install then as follows:

```py
py -m pip install PyMuPDF tk
```

Now, the program as follows:

```py
import fitz  # PyMuPDF
import tkinter as tk
from PIL import Image, ImageTk


# Function to get mouse position on the canvas
def on_click(event):
    canvas_x, canvas_y = event.x, event.y
    print(f"Cursor position on canvas: ({canvas_x}, {canvas_y})")

    # Map canvas coordinates to PDF coordinates
    pdf_x = (canvas_x / canvas_width) * page_width
    pdf_y = (
        (canvas_height - canvas_y) / canvas_height * page_height
    )  # Adjust for inverted y-axis
    print(f"Mapped PDF coordinates: ({pdf_x}, {pdf_y})")


# Load the PDF and get the first page, prompt if error
try:
    pdf_path = "sample.pdf"
    document = fitz.open(pdf_path)
    page = document.load_page(0)
except Exception as e:
    print(f"Error loading PDF: {e}")
    
#pdf_path = input("Enter the path to the PDF file: ")

document = fitz.open(pdf_path)
page = document.load_page(0)

# Render the page to an image
pix = page.get_pixmap()
img = Image.frombytes("RGB", [pix.width, pix.height], pix.samples)

# Create a tkinter window
root = tk.Tk()
root.title("PDF Viewer")

# Convert the image to a format tkinter can use
tk_img = ImageTk.PhotoImage(img)

# Create a canvas and add the image to it
canvas = tk.Canvas(root, width=pix.width, height=pix.height)
canvas.pack()
canvas.create_image(0, 0, anchor=tk.NW, image=tk_img)

# Bind the mouse click event to the on_click function
canvas.bind("<Button-1>", on_click)

# Get canvas dimensions (same as image dimensions)
canvas_width, canvas_height = pix.width, pix.height

# Get PDF page dimensions
page_width = page.rect.width
page_height = page.rect.height
print(f"PDF Page dimensions: {page_width}x{page_height}")

# Start the tkinter main loop
root.mainloop()
```

> Link to [Github](https://github.com/keanteng/live-pdf-coordinate)