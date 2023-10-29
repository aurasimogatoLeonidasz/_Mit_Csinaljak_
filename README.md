# _Mit_Csinaljak_
#TodoList
#asznált modulok: tkinter, xml.etree.ElementTree , todolist.py
#  Oszlytályok: TeendoListam, Frame
#   Függvények: __init__, super(), teendo_hozzaadas(), teendo_torles(), teendok_mentes(), teendok_betoltes, main()
#
#main.py
import tkinter as tk
from todoList import TeendoListam

def main():
    root = tk.Tk()
    todo_list = TeendoListam(root)
    todo_list.pack()
    root.mainloop()

if __name__ == "__main__":
    main()

#todolist.py
import tkinter as tk
import xml.etree.ElementTree as ET

class TeendoListam(tk.Frame):
    def __init__(self, master):
        super().__init__(master)
        self.master = master
        master.title("Teendők Listája")

        self.teendok = []

        self.bevitel_mezo = tk.Entry(self)
        self.hozzaadas_gomb = tk.Button(self, text="Hozzáadás", command=self.teendo_hozzaadas)
        self.megjelenito_mezo = tk.Listbox(self, height=10, width=30)
        self.cimke = tk.Label(self, text="Nincs változás a listában")
        self.torles_gomb = tk.Button(self, text="Törlés", command=self.teendo_torles)
        self.kilepes_gomb = tk.Button(self, text="Inkább hagyjuk", command=master.quit)

        self.bevitel_mezo.grid(row=0, column=0, columnspan=2)
        self.hozzaadas_gomb.grid(row=0, column=2)
        self.megjelenito_mezo.grid(row=1, column=0, columnspan=3)
        self.cimke.grid(row=2, column=0, columnspan=3)
        self.torles_gomb.grid(row=3, column=0)
        self.kilepes_gomb.grid(row=3, column=2)

        self.teendok_betoltes()

    def teendo_hozzaadas(self):
        teendo = self.bevitel_mezo.get()
        if teendo:
            self.teendok.append(teendo)
            self.teendok_frissitese()
            self.cimke.config(text="Hozzáadva: " + teendo)
            self.bevitel_mezo.delete(0, "end")
            self.teendok_mentese()

    def teendo_torles(self):
        selected_index = self.megjelenito_mezo.curselection()
        if selected_index:
            index = selected_index[0]
            deleted_item = self.teendok.pop(index)
            self.teendok_frissitese()
            self.cimke.config(text="Törölve: " + deleted_item)
            self.teendok_mentese()

    def teendok_frissitese(self):
        self.megjelenito_mezo.delete(0, "end")
        for item in self.teendok:
            self.megjelenito_mezo.insert("end", item)

    def teendok_mentese(self):
        root = ET.Element("TeendoListam")
        for entry in self.teendok:
            item = ET.Element("Teendo")
            item.text = entry
            root.append(item)

        tree = ET.ElementTree(root)
        with open("teendok.xml", "wb") as file:
            tree.write(file)

    def teendok_betoltes(self):
        try:
            tree = ET.parse("teendok.xml")
            root = tree.getroot()
            for item in root:
                self.teendok.append(item.text)
                self.teendok_frissitese()
        except FileNotFoundError:
            pass
