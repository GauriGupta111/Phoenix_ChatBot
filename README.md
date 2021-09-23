# Phoenix_ChatBot
# Phoenix is a ChatBot that takes user query input via text and than answer the query from google or wikipedia in the form of text and speech.
from tkinter import *
import wikipedia
from chatterbot import ChatBot
from PIL import ImageTk, Image
import pyttsx3
import time
import requests

class ENGSM:
    ISO_639_1 = 'en_core_web_sm'


import requests

headers = {
    "apikey": "7ea9af30-ff54-11eb-8334-35f6e49ce1fa"}


bot = ChatBot("Phoenix", tagger_language=ENGSM)
engine = pyttsx3.init()
print("Ask to Bot")


main = Tk()
main.configure(bg="gray100")
main.geometry("500x700")
main.title("My ChatBot")


def ask_bot():
    query = user_input.get()
    chatBox.insert(END, "You :" + query)
    # answer=google.search(query, 1)
    params = (
        ("q", query),
    )
    answer, answer2 = "", ""
    try:
        answer2 = wikipedia.summary(query, sentences=2)
        print(answer2)
    except wikipedia.exceptions.PageError:
        print("wikipedia has no result for:", query)

    try:
        answer = requests.get('https://app.zenserp.com/api/v2/search', headers=headers, params=params);
        res = answer.json()['organic']
        for dta in res:
            try:
                answer = dta['description']
                break
            except Exception as e:
                print(e)
                answer = ""
        print(answer)
    except:
        answer = ""

    if answer[-3:-1] + answer[-1] == "...":
        lst = answer.split(".")
        answer = ".".join(lst[0:-3])

    if answer2 and answer:
        chatBox.insert(END, "Bot  :" + str(answer2))
        chatBox.insert(END, "OR")
        chatBox.insert(END, str(answer))
    elif not answer2 and not answer:
        chatBox.insert(END, "Bot  :" + "I dont know. You can search it on google.")
    else:
        chatBox.insert(END, "Bot  :" + str(answer))
    engine.say(str(answer))
    user_input.delete(0, END)
    chatBox.yview(END)
    engine.runAndWait()


img = ImageTk.PhotoImage(Image.open("Phoenix.png"))
lblPhoto = Label(main, image=img)
lblPhoto.pack(pady=5)

frame = Frame(main)
scrollbar = Scrollbar(frame, relief=RIDGE, bd=5)
chatBox = Listbox(frame, yscrollcommand=scrollbar.set, relief=RIDGE, bd=5, bg="White", fg="cyan4", width=500, height=21,
                  font=("Arial", 16))
scrollbar.pack(side=RIGHT, fill=Y, pady=5)
chatBox.pack(side=LEFT, fill=BOTH, pady=5)
scrollbar.config(command=chatBox.yview)
frame.pack()

user_input = Entry(main, borderwidth=5, relief=RIDGE, fg="cyan4", font=("Arial", 20))
user_input.pack(fill=X)

btn = Button(main, highlightthickness=3, fg="White", width=8, height=1, text="Ask", font=("Arial", 22), command=ask_bot)
btn.config(highlightbackground="cyan4", highlightcolor="cyan4")
btn.pack(pady=5)

main.mainloop()
