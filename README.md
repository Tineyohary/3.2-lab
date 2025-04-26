import flet as ft
import json
import os

class Model:
    def __init__(self):
        self._a = 0
        self._b = 0
        self._c = 0
        self.load_state()  # Загружаем сохранённые значения при инициализации

    def load_state(self):
        try:
            if os.path.exists('state.json'):
                with open('state.json', 'r') as f:
                    data = json.load(f)
                    self._a = data.get('a', 0)
                    self._b = data.get('b', 0)
                    self._c = data.get('c', 0)
        except Exception as e:
            print(f"Error loading state: {e}")

    def save_state(self):
        try:
            with open('state.json', 'w') as f:
                json.dump({
                    'a': self._a,
                    'b': self._b,
                    'c': self._c
                }, f)
        except Exception as e:
            print(f"Error saving state: {e}")

    # Геттеры для значений
    def get_a(self):
        return self._a

    def get_b(self):
        return self._b

    def get_c(self):
        return self._c

    # Проверка на то, что наше значение (число) входит в диапазон от 0 до 100
    def redaction(self, value):
        if value > 100:
            value = 100
        if value < 0:
            value = 0
        return value

    # Проверка на корректность значений (Разрешающее поведение для А) a<=b<=c
    def set_a(self, value):
        value = int(value)
        value = self.redaction(value)
        if value > self._b:
            self._b = value
        if value > self._c:
            self._c = value
        self._a = value
        self.save_state()  # Сохраняем состояние после изменения

    # Проверка на корректность значений (Разрешающее поведение для B) a<=b<=c
    def set_b(self, value):
        value = int(value)
        value = self.redaction(value)
        if value < self._a:
            value = self._a
        
        if value > self._c:
            value = self._c
        self._b = value
        self.save_state()  # Сохраняем состояние после изменения

    # Проверка на корректность значений (Разрешающее поведение для C) a<=b<=c
    def set_c(self, value):
        value = int(value)
        value = self.redaction(value)
        if value < self._a:
            self._a = value
        if value < self._b:
            self._b = value
        self._c = value
        self.save_state()  # Сохраняем состояние после изменения

def main(page: ft.Page):
    page.title = 'MVC with State Persistence'
    page.theme_mode = 'light'
    page.vertical_alignment = ft.MainAxisAlignment.START

    # Создаём модель (она автоматически загрузит сохранённые значения)
    model = Model()

    # Текстовые поля для счётчиков
    txt_number1 = ft.TextField(
        value=str(model.get_a()), text_align=ft.TextAlign.RIGHT, width=100)
    txt_number2 = ft.TextField(
        value=str(model.get_b()), text_align=ft.TextAlign.RIGHT, width=100)
    txt_number3 = ft.TextField(
        value=str(model.get_c()), text_align=ft.TextAlign.RIGHT, width=100)

    # Отображение букв над полями
    text1 = ft.Text(value="A", size=30)
    text2 = ft.Text(value="B", size=30)
    text3 = ft.Text(value="C", size=30)

    # Текстовые поля
    tb1 = ft.TextField(value=str(model.get_a()))
    tb2 = ft.TextField(value=str(model.get_b()))
    tb3 = ft.TextField(value=str(model.get_c()))

    # Слайдеры
    sl1 = ft.Slider(min=0, max=100, divisions=100, value=model.get_a())
    sl2 = ft.Slider(min=0, max=100, divisions=100, value=model.get_b())
    sl3 = ft.Slider(min=0, max=100, divisions=100, value=model.get_c())

    # Функции для кнопок
    def minus_click1(e):
        txt_number1.value = str(int(txt_number1.value) - 1)
        model.set_a(txt_number1.value)
        update_ui()
        page.update()

    def plus_click1(e):
        txt_number1.value = str(int(txt_number1.value) + 1)
        model.set_a(txt_number1.value)
        update_ui()
        page.update()

    def minus_click2(e):
        txt_number2.value = str(int(txt_number2.value) - 1)
        model.set_b(txt_number2.value)
        update_ui()
        page.update()

    def plus_click2(e):
        txt_number2.value = str(int(txt_number2.value) + 1)
        model.set_b(txt_number2.value)
        update_ui()
        page.update()

    def minus_click3(e):
        txt_number3.value = str(int(txt_number3.value) - 1)
        model.set_c(txt_number3.value)
        update_ui()
        page.update()

    def plus_click3(e):
        txt_number3.value = str(int(txt_number3.value) + 1)
        model.set_c(txt_number3.value)
        update_ui()
        page.update()

    # Обработчики изменений
    def on_a_changed(e):
        try:
            value2 = int(tb1.value)
            model.set_a(value2)
            update_ui()
        except ValueError:
            pass

    def on_b_changed(e):
        try:
            value2 = int(tb2.value)
            model.set_b(value2)
            update_ui()
        except ValueError:
            pass

    def on_c_changed(e):
        try:
            value2 = int(tb3.value)
            model.set_c(value2)
            update_ui()
        except ValueError:
            pass

    def on_txt_number1_changed(e):
        try:
            value = int(txt_number1.value)
            model.set_a(value)
            update_ui()
        except ValueError:
            pass

    def on_txt_number2_changed(e):
        try:
            value = int(txt_number2.value)
            model.set_b(value)
            update_ui()
        except ValueError:
            pass

    def on_txt_number3_changed(e):
        try:
            value = int(txt_number3.value)
            model.set_c(value)
            update_ui()
        except ValueError:
            pass

    # Функция для обновления интерфейса
    def update_ui():
        txt_number1.value = str(model.get_a())
        txt_number2.value = str(model.get_b())
        txt_number3.value = str(model.get_c())
        tb1.value = str(model.get_a())
        tb2.value = str(model.get_b())
        tb3.value = str(model.get_c())
        sl1.value = model.get_a()
        sl2.value = model.get_b()
        sl3.value = model.get_c()
        page.update()

    # Привязка обработчиков событий
    txt_number1.on_change = on_txt_number1_changed
    txt_number2.on_change = on_txt_number2_changed
    txt_number3.on_change = on_txt_number3_changed
    tb1.on_change = on_a_changed
    tb2.on_change = on_b_changed
    tb3.on_change = on_c_changed
    sl1.on_change = lambda e: (model.set_a(int(sl1.value)), update_ui())
    sl2.on_change = lambda e: (model.set_b(int(sl2.value)), update_ui())
    sl3.on_change = lambda e: (model.set_c(int(sl3.value)), update_ui())

    # Добавление элементов в приложение
    page.add(
        ft.Row(
            [
                ft.Column(
                    [
                        text1, tb1,
                        ft.Row(
                            [
                                txt_number1,
                                ft.Column(
                                    [
                                        ft.IconButton(ft.icons.REMOVE, on_click=minus_click1),
                                        ft.IconButton(ft.icons.ADD, on_click=plus_click1)
                                    ],
                                ),
                            ], alignment=ft.MainAxisAlignment.CENTER),
                        sl1,
                    ],
                    horizontal_alignment=ft.CrossAxisAlignment.CENTER,
                ),
                ft.Column(
                    [
                        text2, tb2,
                        ft.Row(
                            [
                                txt_number2,
                                ft.Column(
                                    [
                                        ft.IconButton(ft.icons.REMOVE, on_click=minus_click2),
                                        ft.IconButton(ft.icons.ADD, on_click=plus_click2),
                                    ],
                                ),
                            ]),
                        sl2,
                    ],
                    horizontal_alignment=ft.CrossAxisAlignment.CENTER,
                ),
                ft.Column(
                    [
                        text3, tb3,
                        ft.Row(
                            [
                                txt_number3,
                                ft.Column(
                                    [
                                        ft.IconButton(ft.icons.REMOVE, on_click=minus_click3),
                                        ft.IconButton(ft.icons.ADD, on_click=plus_click3),
                                    ],
                                ),
                            ]),
                        sl3,
                    ],
                    horizontal_alignment=ft.CrossAxisAlignment.CENTER,
                )
            ], alignment=ft.MainAxisAlignment.CENTER
        ),
    )

ft.app(target=main)
