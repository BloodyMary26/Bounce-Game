# Bounce-Game
A classical game for programmers

from tkinter import *
import random
import time

class Ball:

    def __init__(self, canvas, paddle, score, color): #traits
        self.canvas = canvas
        self.paddle = paddle
        self.score = score
        self.id = canvas.create_oval(10, 10, 25, 25, fill = color) #identificator
        self.canvas.move(self.id, 245, 100) #first position
        starts = [-3, -2, -1, 1, 2, 3] #random start point
        random.shuffle(starts)
        self.x = starts[0]
        self.y = -3 #going up at the beginning
        self.canvas_height = self.canvas.winfo_height() #current height/width of the canvas
        self.canvas_width = self.canvas.winfo_width()
        self.hit_bottom = False

    def hit_paddle(self, pos):
        paddle_pos = self.canvas.coords(self.paddle.id)
        if pos[2] >= paddle_pos[0] and pos[0] <= paddle_pos[2]: #width
            if pos[3] >= paddle_pos[1] and pos[3] <= paddle_pos[3]: #height
                self.x += self.paddle.x #horizontal acceleration of the ball
                self.score.hit() #score++
                return True #hit
        return False #no hit

    def draw(self):
        self.canvas.move(self.id, self.x, self.y)
        pos = self.canvas.coords(self.id)
        if pos[1] <= 0: #top
            self.y = 3
        if pos[3] >= self.canvas_height: #bottom
            self.hit_bottom = True #game over
        if self.hit_paddle(pos) == True: #moment of hitting the paddle
            self.y = -3
        if pos[0] <= 0: #left
            self.x = 3 
        if pos[2] >= self.canvas_width: #right
            self.x = -3

class Paddle:

    def __init__(self, canvas, color):
        self.canvas = canvas
        self.id = canvas.create_rectangle(0, 0, 100, 10, fill = color)
        self.canvas.move(self.id, 200, 300) #start point
        self.x = 0 #no movement
        self.canvas_width = self.canvas.winfo_width()
        self.started = False
        self.canvas.bind_all('<KeyPress-Left>', self.turn_left)
        self.canvas.bind_all('<KeyPress-Right>', self.turn_right)
        self.canvas.bind_all('<Button-1>', self.start_game)

    def draw(self):
        self.canvas.move(self.id, self.x, 0)
        pos = self.canvas.coords(self.id)
        if pos[0] <= 0: #left
            self.x = 0 #stop
        elif pos[2] >= self.canvas_width: #right
            self.x = 0

    def turn_left(self, evt):
        self.x = -2

    def turn_right(self, evt):
        self.x = 2

    def start_game(self, evt):
        self.started = True

class Score:

    def __init__(self, canvas, color):
        self.score = 0 #at the beginning
        self.canvas = canvas
        self.id = canvas.create_text(420, 15, text = 'Score: %s' % self.score, fill = color, font = ('Courier', 20))

    def hit(self):
        self.score += 1
        self.canvas.itemconfig(self.id, text = 'Score: %s' % self.score) #update

tk = Tk()
tk.title('Bounce!')
tk.resizable(0, 0) #fix the size
tk.wm_attributes('-topmost', 1) #over the other windows
canvas = Canvas(tk, width = 500, height = 400, bd = 0, highlightthickness = 0) #no frame
canvas.pack()
tk.update() #preparation for the animation

score = Score(canvas, '#ff80c0')
paddle = Paddle(canvas, '#1cae9c')
ball = Ball(canvas, paddle, score, '#8080ff')

while 1:
    if ball.hit_bottom == False and paddle.started == True: #condition of the game
        ball.draw()
        paddle.draw()
    if ball.hit_bottom == True: #the end
        time.sleep(0.05)
        canvas.itemconfig(ball.id, state = 'hidden')
        canvas.itemconfig(paddle.id, state = 'hidden')
        canvas.create_text(250, 200, text = 'GAME OVER', fill = '#408080', font = ('Times', 30))
        canvas.create_text(250, 250, text = 'Nice try!', fill = '#400040', font = ('Times', 20))
    tk.update_idletasks() #main cycle, in order not to close the window
    tk.update()
    time.sleep(0.01)

tk.mainloop()
