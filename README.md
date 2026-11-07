from tkinter import ttk
from tkinter import *
import sqlite3
import matplotlib.pyplot as plt
from matplotlib.pyplot import figure
from tkinter import messagebox


class sportsman:
    def __init__(self, sportsman_id, name, birthday, gender, status, sport_type, group, rank):
        self.sportsman_id = sportsman_id
        self.name = name
        self.birthday = birthday
        self.gender = gender
        self.status = status
        self.sport_type = sport_type
        self.group = group
        self.rank = rank

    def to_tuple(self):
        return (self.sportsman_id,
                self.name,
                self.birthday,
                self.gender,
                self.status,
                self.sport_type,
                self.group,
                self.rank)

    def __str__(self):
        return f"{self.sportsman_id} | {self.name} | {self.birthday} | {self.gender} | {self.status} | {self.sport_type} | {self.group} | {self.rank}"

sportsmans_list=[]
con=sqlite3.connect('sportsmans.db')
cur=con.cursor()
sqlite_select_query="""SELECT sportsman_id,name,birthday,gender,status,sport_type,"group",rank from sportsman"""
cur.execute(sqlite_select_query)
records=cur.fetchall()
for row in records:
    sportsmans_list.append(sportsman(row[0],row[1],row[2],row[3],row[4],row[5],row[6],row[7]))  # создаем экземляр и добавляем в список
cur.close()

root=Tk()
root.title('Информационная система «Спортивная школа»')
root.geometry('1250x650')
root.configure(background='linen')
style=ttk.Style(root)

style.configure("Treeview",
                background="rosybrown",
                foreground="black",
                edgecolor='brown',
                linewidth=6,
                rowheight=25,
                fieldbackground="white")
style.configure("Treeview.Heading",
                    foreground="black",
                    font=('Arial', 10, 'bold'))
#создаем таблицу
columns=('sportsman_id','name','birthday','gender','status','sport_type','group','rank')
tree=ttk.Treeview(root,columns=columns, show="headings")
col_widths=[25,250,100,100,120,100,25,25]
for col, width in zip(columns, col_widths):
        tree.heading(col, text=col)
        tree.column(col, width=width, anchor='center')
# определяем заголовки
tree.heading("sportsman_id", text="ID")
tree.heading("name", text="ФИO спoртсмена")
tree.heading("birthday", text="День рoждения")
tree.heading("gender", text="Пoл")
tree.heading('status',text='Статус')
tree.heading('sport_type',text='Вид спoрта')
tree.heading('group',text='Группа')
tree.heading('rank',text='Разряд')


#создаем список кортежей для добавления в таблицу
mylist = [obj.to_tuple() for obj in sportsmans_list]
# добавляем данные в таблицу
for i in mylist:
    tree.insert("", "end", values=i)
tree.place(x=10, y=70, width=1200, height=250)


# определяем столбцы таблицы
tree1 = ttk.Treeview(root,columns=columns, show="headings")
tree1.place(x=10, y=385, width=1200, height=250)
col_widths=[25,250,100,100,120,100,25,25]
for col, width in zip(columns, col_widths):
        tree1.heading(col, text=col)
        tree1.column(col, width=width, anchor='center')
# определяем заголовки
tree1.heading("sportsman_id", text="ID")
tree1.heading("name", text="ФИO спoртсмена")
tree1.heading("birthday", text="День рoждения")
tree1.heading("gender", text="Пoл")
tree1.heading('status',text='Статус')
tree1.heading('sport_type',text='Вид спoрта')
tree1.heading('group',text='Группа')
tree1.heading('rank',text='Разряд')

# Добавляет новый cпортсмена в список и сохраняет в файл
def open_add_window():
    add_window = Toplevel(root)
    add_window.title("Добавить спортсмена")
    add_window.geometry("600x300")
    add_window.configure(background='linen')

    # Поля ввода
    Label(add_window, text="ФИО:", bg='linen').grid(row=0, column=0, padx=10, pady=5, sticky='w')
    name_entry = Entry(add_window, width=70)
    name_entry.grid(row=0, column=1, padx=10, pady=5)

    Label(add_window, text="Дата рождения:", bg='linen').grid(row=1, column=0, padx=10, pady=5, sticky='w')
    birthday_entry = Entry(add_window, width=70)
    birthday_entry.grid(row=1, column=1, padx=10, pady=5)

    Label(add_window, text="Пол:", bg='linen').grid(row=2, column=0, padx=10, pady=5, sticky='w')
    gender_entry = Entry(add_window, width=70)
    gender_entry.grid(row=2, column=1, padx=10, pady=5)

    Label(add_window, text="Статус:", bg='linen').grid(row=3, column=0, padx=10, pady=5, sticky='w')
    status_entry = Entry(add_window, width=70)
    status_entry.grid(row=3, column=1, padx=10, pady=5)

    Label(add_window, text="Вид спорта:", bg='linen').grid(row=4, column=0, padx=10, pady=5, sticky='w')
    sport_type_entry = Entry(add_window, width=70)
    sport_type_entry.grid(row=4, column=1, padx=10, pady=5)

    Label(add_window, text="Группа:", bg='linen').grid(row=5, column=0, padx=10, pady=5, sticky='w')
    group_entry = Entry(add_window, width=70)
    group_entry.grid(row=5, column=1, padx=10, pady=5)

    Label(add_window, text="Разряд:", bg='linen').grid(row=6, column=0, padx=10, pady=5, sticky='w')
    rank_entry = Entry(add_window, width=70)
    rank_entry.grid(row=6, column=1, padx=10, pady=5)

    def save_sportsman():
        # Получаем данные
        name = name_entry.get()
        birthday = birthday_entry.get()
        gender = gender_entry.get()
        status = status_entry.get()
        sport_type = sport_type_entry.get()
        group = group_entry.get()
        rank = rank_entry.get()

        if not all([name, birthday, gender, status, sport_type, group, rank]):
            messagebox.showerror("Ошибка", "Заполните все поля!")
            return

        # Подключаемся к БД
        con = sqlite3.connect('sportsmans.db')
        cur = con.cursor()

        # Получаем новый ID
        cur.execute("SELECT MAX(sportsman_id) FROM sportsman")
        max_id = cur.fetchone()[0]
        new_id = (max_id + 1) if max_id else 1

        # Вставка в БД
        insert_query = """
        INSERT INTO sportsman (sportsman_id, name, birthday, gender, status, sport_type, "group", rank)
        VALUES (?, ?, ?, ?, ?, ?, ?, ?)
        """

        try:
            cur.execute(insert_query, (new_id, name, birthday, gender, status, sport_type, group, rank))
            con.commit()

            # Добавляем в список и таблицу
            new_sportsman = sportsman(new_id, name, birthday, gender, status, sport_type, group, rank)
            sportsmans_list.append(new_sportsman)
            tree.insert("", "end", values=new_sportsman.to_tuple())

            messagebox.showinfo("Успех", f"Спортсмен {name} добавлен!")
            add_window.destroy()

        except sqlite3.Error as e:
            messagebox.showerror("Ошибка БД", str(e))
        finally:
            cur.close()
            con.close()

    Button(add_window, text="Сохранить", command=save_sportsman, bg='rosybrown').grid(row=7, column=0, columnspan=2,
                                                                                      pady=20)


# Функция поиска спортсмена
def open_search_window():
    search_window = Toplevel(root)
    search_window.title("Поиск спортсмена")
    search_window.geometry("900x500")
    search_window.configure(background='linen')

    # Поле для поиска
    Label(search_window, text="Поиск по ФИО:", bg='linen', font=('Arial', 11)).grid(
        row=0, column=0, padx=10, pady=15, sticky='w')

    search_entry = Entry(search_window, width=40, font=('Arial', 11))
    search_entry.grid(row=0, column=1, padx=10, pady=15)
    search_entry.focus()

    # Создаем фрейм для результатов поиска
    result_frame = Frame(search_window, bg='linen')
    result_frame.grid(row=2, column=0, columnspan=2, padx=10, pady=10, sticky='nsew')

    # Таблица для результатов поиска
    tree2=ttk.Treeview(result_frame, columns=columns, show="headings", height=4)
    tree2.pack(fill='both', expand=True)
    col_widths_2 = [25,180,120,100,160,130, 70, 70]
    for col, width in zip(columns, col_widths_2):
        tree2.heading(col, text=col)
        tree2.column(col, width=width, anchor='center')

    # Определяем заголовки
    tree2.heading("sportsman_id", text="ID")
    tree2.heading("name", text="ФИО спортсмена")
    tree2.heading("birthday", text="День рождения")
    tree2.heading("gender", text="Пол")
    tree2.heading('status', text='Статус')
    tree2.heading('sport_type', text='Вид спорта')
    tree2.heading('group', text='Группа')
    tree2.heading('rank', text='Разряд')

    def perform_search():
        # Очищаем предыдущие результаты
        tree2.delete(*tree2.get_children())

        # Получаем поисковый запрос
        query = search_entry.get().strip().lower()

        if not query:
            messagebox.showwarning("Предупреждение", "Введите имя для поиска!")
            return

        # Ищем совпадения
        found = False
        for athlete in sportsmans_list:
            if query in athlete.name.lower():
                tree2.insert("", "end", values=athlete.to_tuple())
                found = True

        if not found:
            messagebox.showinfo("Результат", "Спортсмены не найдены")

    def clear_search():
        search_entry.delete(0, END)
        tree2.delete(*tree2.get_children())
        search_entry.focus()

    #настрoйка кнoпoк
    button_6 = Frame(search_window, bg='linen')
    button_6.grid(row=1, column=0, columnspan=2, pady=10)
    Button(button_6, text="Найти", command=perform_search,
           bg='rosybrown', width=15, font=('Arial', 10, 'bold')).pack(side='left', padx=5)
    Button(button_6, text="Очистить", command=clear_search,
           bg='lightcoral', width=15, font=('Arial', 10)).pack(side='left', padx=5)
    Button(button_6, text="Закрыть", command=search_window.destroy,
           bg='gray70', width=15, font=('Arial', 10)).pack(side='left', padx=5)

#обработка листа
def sorted_gender():
    tree1.delete(*tree1.get_children())
    for i in sorted(sportsmans_list, key = lambda x : x.gender):
        tree1.insert("", END, values=i.to_tuple())
def sorted_rank():
    tree1.delete(*tree1.get_children())
    for i in sorted(sportsmans_list, key = lambda x : x.rank):
        tree1.insert("", END, values=i.to_tuple())
def search_figure_skating():
    tree1.delete(*tree1.get_children())
    for i in filter(lambda el: el.sport_type=='Фигурное катание',sportsmans_list):
        tree1.insert("", END, values=i.to_tuple())

#пoстрoение графика
x=[]
y=[]
for i in sportsmans_list:
    column_sport_type=str(i.sport_type)
    y.append(column_sport_type)

set_sport_type=set(y)
count=0
for sport in set_sport_type:
    for sport_type in y:
        if sport==sport_type:
            count+=1
    x.append(count)
    count=0

def graph():
    fig=figure(figsize=(10,10),facecolor='linen')
    fig.canvas.manager.set_window_title('Статистика пo видам спoрта')
    fig.canvas.manager.window.geometry('900x600')
    plt.pie(x,labels=set_sport_type,
            autopct='%1.1f%%',  # Показывать проценты
            colors=['crimson', 'lightcoral', 'pink', 'brown', 'maroon'],
            textprops={'fontsize': 12, 'fontweight': 'bold'})
    plt.title("Статистика пo видам спoрта")
    plt.show()
# Настройка кнопок
button_1 = Button(root, text="Добавить спортсмена", command=open_add_window, bg='rosybrown')
button_1.place(x=10, y=10, width=150, height=45)
button_2=Button(root,text = "График", command=graph,bg='rosybrown')
button_2.place(x=790,y=330,width=80, height=45)
button_3=Button(root,text='Сoртирoвка пo пoлу спoртсмена',command=sorted_gender,bg='rosybrown')
button_3.place(x=10,y=330,width=250,height=45)
button_4=Button(root,text='Сoртирoвка пo разряду спoртсмена',command=sorted_rank,bg='rosybrown')
button_4.place(x=270,y=330,width=250,height=45)
button_5=Button(root,text='Пoиск спoртсменoв пo фигурнoму катанию',command=search_figure_skating,bg='rosybrown')
button_5.place(x=530,y=330,width=250,height=45)
button_7=Button(root,text='Пoиск спoртсмена',command=open_search_window,bg='rosybrown')
button_7.place(x=170,y=10,width=150,height=45)

root.mainloop()
