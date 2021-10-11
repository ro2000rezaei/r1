# r1

from tkinter import *
from tkinter import ttk
import tkinter as tk
import numpy as np
import cv2
import time
import win32com.client
import winsound

# for beep sound (with winsound)
frequency = 1400
delay = 700

# for say movement detection(for speaking)(with win32com.client)
speaker = win32com.client.Dispatch("SAPI.SpVoice")

####################### set name and password to open the app
password = [("roghayeh","1379"),("admin","2000")]
# password = ["8125334"]
###################### define video capture for Entry Bottun and set for it
def show_access():
	# if ((e.get() == name[0] and e2.get() == password[0])) or ((e.get() == name[1] and e2.get() == password[1])):
	if (e.get() and e2.get() in password[0]) or (e.get() and e2.get() in password[1]):
		
		############## movement detection

		cap = cv2.VideoCapture(0)

		speaker.Speak("well com to program Permitted Access")
		while True:
			ret, frame1 = cap.read()
			############## movement detection
			ret, frame2 = cap.read()
			gray1 = cv2.cvtColor(frame1, cv2.COLOR_BGR2GRAY)
			gray2 = cv2.cvtColor(frame2, cv2.COLOR_BGR2GRAY)
			blur1 = cv2.GaussianBlur(gray1,(21,21), 0)
			blur2 = cv2.GaussianBlur(gray2,(21,21), 0)
			diff = cv2.absdiff(blur1,blur2)

			ret, thresh = cv2.threshold(diff, 20, 255, cv2.THRESH_BINARY)
			# count the number of white pixels in threshold image 
			white_pixel = np.sum(thresh) / 255

			rows, cols = thresh.shape
			total = rows * cols
			if white_pixel > 0.00000000000000000001 * total:
				font = cv2.FONT_HERSHEY_SIMPLEX
				cv2.putText(frame1,"Movement Detection",(10,40),font, 0.5,(255,255,255),2)
				speaker.Speak("warning  earthquike")

			###################### show time & illegal access
			font = cv2.FONT_HERSHEY_SIMPLEX
			cv2.putText(frame1,time.ctime(),(10,50),font,0.5,(255,255,255),2)
			cv2.putText(frame1,"Permitted Access",(10,20),font,0.8,(0,255,0),2)
			cv2.putText(frame1,"press Q=EXIT",(10,80),font,0.8,(0,255,200),1)

			cv2.imshow("frame1",frame1)
			if cv2.waitKey(5) & 0xFF == ord("q"):
				break

		cap.release()
		cv2.destroyAllWindows()
		########################################
	else:
		######################################## save video 
		cap = cv2.VideoCapture(0)
		speaker.Speak("warning")
		fourcc = cv2.VideoWriter_fourcc(*"XVID")
		out = cv2.VideoWriter("out.avi",fourcc, 5.0, (640,480))
		while True:
			ret, frame = cap.read()
			
			###################### show time & illegal access
			font = cv2.FONT_HERSHEY_SIMPLEX
			cv2.putText(frame,time.ctime(),(10,60),font,0.7,(55,130,30),1)
			cv2.putText(frame," Illegal Access !",(10,20),font,0.8,(0,0,255),2)
			cv2.putText(frame,"press q for exit the program",(10,40),font,0.7,(0,255,200),1)

			winsound.Beep(frequency ,delay)

			# save video & show it
			out.write(frame)
			cv2.imshow("frame",frame)
			if cv2.waitKey(5) & 0xFF == ord("q"):
				break

		cap.release()
		out.release()
		cv2.destroyAllWindows()

###################### define Bottun in tkinter 
######
# create frame
win = tk.Tk()
win.title("roghayeh rezaei")
win.geometry("260x100")
#### label for Buttom
ttk.Label(win, text="Your Name").grid(row=0, sticky = tk.W, pady = 5)
ttk.Label(win, text="Your Password ").grid(row=1, sticky = tk.W, pady = 5)

#### create entry
e = ttk.Entry(win)
e2 = ttk.Entry(win)

e.grid(row=1, column=1)

e2.grid(row=0, column=1)
##### set action for Buttom
ex = ttk.Button(win,text='Exit',command = win.quit)
ex.grid(row = 2, column = 0, sticky = tk.E, pady = 1)

en = ttk.Button(win,text='Enter', command = show_access)
en.grid(row = 2, column = 1, sticky = tk.N, pady = 1)
win.mainloop()
