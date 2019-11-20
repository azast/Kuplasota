# Kuplasota
#Alkuimportit
from tkinter import *
from random import randint
from time import time as t
from time import sleep
from math import sqrt
import pygame
from pygame import *
BUB_CHANCE = 10
TIME_LIMIT = 31
end = t() + TIME_LIMIT
HEIGHT = 500
WIDTH = 800
window = Tk()
window.title('Kuplasota')
c = Canvas(window, width=WIDTH, height=HEIGHT, bg='darkblue')
c.pack()
pygame.init()
pygame.mixer.music.load('daydream.mp3')
pygame.mixer.music.play(0)
#Kuplien luominen, ensiksi listat johon tallennetaan kuplien henk tiedot
#Kuplan numero
bub_id = list()
#Kuplan koko
bub_r = list()
#Kuplan nopeus
bub_spd = list()
#MAX BUB NOPEUDET JA KOOT
MIN_BUB_R = 10
MAX_BUB_R = 30
MAX_BUB_SPD = 10
#GAP
GAP = 100
#Laivat ja niitden luominen
ship_id1 = c.create_polygon(5, 5, 5, 25, 30, 15, fill='red')
ship_id2 = c.create_oval(0, 0, 30, 30, outline='red')
#Sukellusveneen säde koko
SHIP_R = 15
#Näytön keskipisteen koordinaatti muuttujat
MID_X = WIDTH / 2
MID_Y = HEIGHT / 2
#Laivat siirretään keskelle
c.move(ship_id1, MID_X, MID_Y)
c.move(ship_id2, MID_X, MID_Y)
SHIP_SPD = 10
#fUNKTIO JOKA MÄÄRITTÄÄ NUOLEN PAINAMISEN JA SEN VAIKUTUKSEN
def move_ship(event):
    if event.keysym == 'Up':
        c.move(ship_id1, 0, -SHIP_SPD)
        c.move(ship_id2, 0, -SHIP_SPD)
    elif event.keysym == 'Down':
        c.move(ship_id1, 0, SHIP_SPD)
        c.move(ship_id2, 0, SHIP_SPD)
    elif event.keysym == 'Left':
        c.move(ship_id1, -SHIP_SPD, 0)
        c.move(ship_id2, -SHIP_SPD, 0)
    elif event.keysym == 'Right':
        c.move(ship_id1, SHIP_SPD, 0)
        c.move(ship_id2, SHIP_SPD, 0)
#Toista aina painettaessa
c.bind_all('<Key>', move_ship)
#Kuplan luomisen funktio
def create_bubble():
    x = WIDTH + GAP
    y = randint(0, HEIGHT)
    r = randint(MIN_BUB_R, MAX_BUB_R)
    id1 = c.create_oval(x - r, y - r, x + r, y + r, outline='white')
    bub_id.append(id1)
    bub_r.append(r)
    bub_spd.append(randint(1, MAX_BUB_SPD))
#Kuplien liikkumisen funktio
def move_bubbles():
    for i in range(len(bub_id)):
        c.move(bub_id[i], -bub_spd[i], 0)
#Kuplien sijainnin selvittäminen id perusteella
def get_coords(id_num1):
    pos = c.coords(id_num1)
#Tämä laskee kuplan keskipisteen x koordinaatit
    x = (pos[0] + pos[2])/2
    y = (pos[1] + pos[3])/2
    return x, y
#Tämä funktio poistaa kuplan jonka tunnusnumero on i
def del_bubble(i):
    del bub_r[i]
    del bub_spd[i]
    c.delete(bub_id[i])
    del bub_id[i]
#Funktio joka poistaa näytön ulkona olevat kuplat
def clean_up_bubs():
    for i in range(len(bub_id)-1, -1, -1):
        x, y = get_coords(bub_id[i])
        if x < -GAP:
            del_bubble(i)
#Laskee alusten etäisyyden toisistaan
def distance(id1, id2):
    x1, y1 = get_coords(id1)
    x2, y2 = get_coords(id2)
    return sqrt((x2 - x1)**2 + (y2 -y1)**2)
def collision():
    points = 0
    for bub in range(len(bub_id)-1, -1, -1):
        if distance(ship_id2, bub_id[bub]) < (SHIP_R + bub_r[bub]):
            points += (bub_r[bub] + bub_spd[bub])
            del_bubble(bub)
    return points
c.create_text(50, 30, text='AIKA', fill='white')
c.create_text(150, 30, text='PISTEET', fill='white')
time_text = c.create_text(50, 50, fill='white')
score_text = c.create_text(150, 50, fill='white')
#Näyttää pistemäärän
def show_score(score):
    c.itemconfig(score_text, text=str(score))
#Näyttää ajan
def show_time(time_left):
    c.itemconfig(time_text, text=str(int(time_left)))
score = 0
#Pää Loop
while t() < end:
    if randint(1, BUB_CHANCE) == 1:
        create_bubble()
    move_bubbles()
    clean_up_bubs()
    score += collision()
    show_score(score)
    show_time(end - t())
    window.update()
    sleep(0.01)
c.create_text(MID_X, MID_Y, text='PELI OHI', fill='white', font=('Helvetica', 30))
c.create_text(MID_X, MID_Y + 45, text='PISTEET: ' + str(score), fill='white')

