---
layout: post 
title: Scribbl With users
search_exclude: true
permalink: /home
menu: nav/scribbl_with_users.html
author: Zach
---

## Welcome to Scribbl With Users

This media page allows you to express yourself with hand drawn images, along with attempting to guess what a user draws along with helpful hints along the way. Click through the navigation menu above to get started!

### Drawing Pad

import tkinter as tk
from tkinter.colorchooser import askcolor

class DrawingPad:
    def __init__(self, root):
        self.root = root
        self.root.title("Drawing Pad")

        # Initialize attributes
        self.current_color = "black"
        self.is_eraser = False
        self.undo_stack = []
        self.canvas_width = 800
        self.canvas_height = 600

        # Canvas setup
        self.canvas = tk.Canvas(root, width=self.canvas_width, height=self.canvas_height, bg="white")
        self.canvas.pack()

        # Buttons and controls
        self.controls_frame = tk.Frame(root)
        self.controls_frame.pack(pady=10)

        self.color_buttons = [
            "red", "orange", "yellow", "green", "blue",
            "purple", "pink", "gray", "brown", "black"
        ]
        for color in self.color_buttons:
            button = tk.Button(
                self.controls_frame,
                bg=color,
                width=2, height=1,
                command=lambda col=color: self.set_color(col)
            )
            button.pack(side="left", padx=2)

        self.eraser_button = tk.Button(self.controls_frame, text="Eraser", command=self.toggle_eraser)
        self.eraser_button.pack(side="left", padx=5)

        self.undo_button = tk.Button(self.controls_frame, text="Undo", command=self.undo_last_action)
        self.undo_button.pack(side="left", padx=5)

        self.clear_button = tk.Button(self.controls_frame, text="Clear All", command=self.clear_canvas)
        self.clear_button.pack(side="left", padx=5)

        # Bind canvas events
        self.canvas.bind("<Button-1>", self.start_drawing)
        self.canvas.bind("<B1-Motion>", self.draw)

        self.last_x, self.last_y = None, None

    def set_color(self, color):
        self.is_eraser = False
        self.current_color = color

    def toggle_eraser(self):
        self.is_eraser = not self.is_eraser
        self.current_color = "white" if self.is_eraser else "black"

    def start_drawing(self, event):
        self.last_x, self.last_y = event.x, event.y
        # Save the current state of the canvas for undo
        self.undo_stack.append(self.canvas.create_image(0, 0, anchor="nw", image=self.get_canvas_snapshot()))

    def draw(self, event):
        x, y = event.x, event.y
        if self.last_x and self.last_y:
            self.canvas.create_line(
                self.last_x, self.last_y, x, y,
                fill=self.current_color, width=5
            )
        self.last_x, self.last_y = x, y

    def undo_last_action(self):
        if self.undo_stack:
            last_action = self.undo_stack.pop()
            self.canvas.delete(last_action)

    def clear_canvas(self):
        self.canvas.delete("all")
        self.undo_stack.clear()

    def get_canvas_snapshot(self):
        # Create an image snapshot of the canvas for undo
        self.root.update()
        x = self.root.winfo_rootx() + self.canvas.winfo_x()
        y = self.root.winfo_rooty() + self.canvas.winfo_y()
        width = self.canvas_width
        height = self.canvas_height
        return tk.PhotoImage(file=self.canvas.postscript(x=x, y=y, width=width, height=height))

# Run the application
if __name__ == "__main__":
    root = tk.Tk()
    app = DrawingPad(root)
    root.mainloop()
