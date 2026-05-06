import tkinter as tk
from tkinter import ttk, messagebox
import json
import os

DATA_FILE = "movies.json"

class MovieLibraryApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Movie Library")
        self.root.geometry("850x550")
        self.root.resizable(True, True)

        # Загрузка данных из файла
        self.movies = self.load_movies()

        # --- Поля ввода ---
        frame_input = ttk.LabelFrame(root, text="Добавить фильм", padding=10)
        frame_input.pack(fill="x", padx=10, pady=5)

        ttk.Label(frame_input, text="Название:").grid(row=0, column=0, sticky="w", padx=5, pady=2)
        self.entry_title = ttk.Entry(frame_input, width=30)
        self.entry_title.grid(row=0, column=1, padx=5, pady=2)

        ttk.Label(frame_input, text="Жанр:").grid(row=0, column=2, sticky="w", padx=5, pady=2)
        self.entry_genre = ttk.Entry(frame_input, width=20)
        self.entry_genre.grid(row=0, column=3, padx=5, pady=2)

        ttk.Label(frame_input, text="Год выпуска:").grid(row=1, column=0, sticky="w", padx=5, pady=2)
        self.entry_year = ttk.Entry(frame_input, width=10)
        self.entry_year.grid(row=1, column=1, padx=5, pady=2, sticky="w")

        ttk.Label(frame_input, text="Рейтинг (0–10):").grid(row=1, column=2, sticky="w", padx=5, pady=2)
        self.entry_rating = ttk.Entry(frame_input, width=10)
        self.entry_rating.grid(row=1, column=3, padx=5, pady=2, sticky="w")

        btn_add = ttk.Button(frame_input, text="Добавить фильм", command=self.add_movie)
        btn_add.grid(row=1, column=4, padx=10, pady=5)

        # --- Фильтрация ---
        frame_filter = ttk.LabelFrame(root, text="Фильтрация", padding=10)
        frame_filter.pack(fill="x", padx=10, pady=5)

        ttk.Label(frame_filter, text="Жанр:").grid(row=0, column=0, padx=5, pady=2, sticky="w")
        self.combo_genre = ttk.Combobox(frame_filter, state="readonly", width=20)
        self.combo_genre.grid(row=0, column=1, padx=5, pady=2, sticky="w")
        self.combo_genre.bind("<<ComboboxSelected>>", self.apply_filter)

        ttk.Label(frame_filter, text="Год:").grid(row=0, column=2, padx=5, pady=2, sticky="w")
        self.entry_filter_year = ttk.Entry(frame_filter, width=10)
        self.entry_filter_year.grid(row=0, column=3, padx=5, pady=2, sticky="w")
        self.entry_filter_year.bind("<KeyRelease>", self.apply_filter)  # фильтр при вводе

        btn_clear_filter = ttk.Button(frame_filter, text="Сбросить фильтр", command=self.clear_filter)
        btn_clear_filter.grid(row=0, column=4, padx=10, pady=5)

        # --- Таблица ---
        frame_table = ttk.Frame(root)
        frame_table.pack(fill="both", expand=True, padx=10, pady=5)

        columns = ("title", "genre", "year", "rating")
        self.tree = ttk.Treeview(frame_table, columns=columns, show="headings", height=12)
        self.tree.heading("title", text="Название")
        self.tree.heading("genre", text="Жанр")
        self.tree.heading("year", text="Год")
        self.tree.heading("rating", text="Рейтинг")

        self.tree.column("title", width=300)
        self.tree.column("genre", width=150)
        self.tree.column("year", width=80, anchor="center")
        self.tree.column("rating", width=80, anchor="center")

        scrollbar = ttk.Scrollbar(frame_table, orient="vertical", command=self.tree.yview)
        self.tree.configure(yscrollcommand=scrollbar.set)

        self.tree.pack(side="left", fill="both", expand=True)
        scrollbar.pack(side="right", fill="y")

        # Заполнение таблицы начальными данными
        self.refresh_table()

    # --- Логика данных ---
    def load_movies(self):
        if os.path.exists(DATA_FILE):
            try:
                with open(DATA_FILE, "r", encoding="utf-8") as f:
                    return json.load(f)
            except:
                return []
        return []

    def save_movies(self):
        with open(DATA_FILE, "w", encoding="utf-8") as f:
            json.dump(self.movies, f, ensure_ascii=False, indent=2)

    def validate_input(self, title, genre, year_str, rating_str):
        if not title.strip():
            messagebox.showerror("Ошибка", "Название не может быть пустым.")
            return False
        if not genre.strip():
            messagebox.showerror("Ошибка", "Жанр не может быть пустым.")
            return False

        # Проверка года
        try:
            year = int(year_str)
            if year < 1888 or year > 2100:  # первый фильм ~1888
                raise ValueError
        except:
            messagebox.showerror("Ошибка", "Год должен быть целым числом (например, 1994).")
            return False

        # Проверка рейтинга
        try:
            rating = float(rating_str)
            if rating < 0 or rating > 10:
                raise ValueError
        except:
            messagebox.showerror("Ошибка", "Рейтинг должен быть числом от 0 до 10.")
            return False

        return True

    def add_movie(self):
        title = self.entry_title.get().strip()
        genre = self.entry_genre.get().strip()
        year_str = self.entry_year.get().strip()
        rating_str = self.entry_rating.get().strip()

        if not self.validate_input(title, genre, year_str, rating_str):
            return

        # Добавляем фильм (год и рейтинг уже проверены)
        movie = {
            "title": title,
            "genre": genre,
            "year": int(year_str),
            "rating": float(rating_str)
        }
        self.movies.append(movie)
        self.save_movies()

        # Очистка полей ввода
        self.entry_title.delete(0, tk.END)
        self.entry_genre.delete(0, tk.END)
        self.entry_year.delete(0, tk.END)
        self.entry_rating.delete(0, tk.END)

        self.refresh_table()

    def apply_filter(self, event=None):
        """Фильтрация по жанру и году (может быть пустым)"""
        genre_filter = self.combo_genre.get()
        year_filter = self.entry_filter_year.get().strip()

        # Заполнить список жанров для комбобокса (если изменился)
        genres = sorted(set(m["genre"] for m in self.movies))
        self.combo_genre["values"] = [""] + genres  # пустой вариант для сброса

        filtered = self.movies

        if genre_filter:
            filtered = [m for m in filtered if m["genre"] == genre_filter]

        if year_filter:
            try:
                year_val = int(year_filter)
                filtered = [m for m in filtered if m["year"] == year_val]
            except ValueError:
                # если введено не число, показываем всё (или можно предупредить)
                pass

        self.refresh_table(filtered)

    def clear_filter(self):
        self.combo_genre.set("")
        self.entry_filter_year.delete(0, tk.END)
        self.apply_filter()

    def refresh_table(self, data=None):
        if data is None:
            data = self.movies

        # Очистка таблицы
        for row in self.tree.get_children():
            self.tree.delete(row)

        # Заполнение
        for movie in data:
            self.tree.insert("", tk.END, values=(
                movie["title"],
                movie["genre"],
                movie["year"],
                movie["rating"]
            ))

        # Обновление списка жанров после любого обновления
        genres = sorted(set(m["genre"] for m in self.movies))
        self.combo_genre["values"] = [""] + genres

if __name__ == "__main__":
    root = tk.Tk()
    app = MovieLibraryApp(root)
    root.mainloop()
