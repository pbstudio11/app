import ssl
import flet as ft

# 解決 macOS 11.3 SSL 證書問題
ssl._create_default_https_context = ssl._create_unverified_context

def main(page: ft.Page):
    page.title = "多功能互動 App"
    page.padding = 20
    page.scroll = ft.ScrollMode.AUTO  # 允許頁面滾動

    # 1. 主題切換邏輯 (切換深色/淺色模式)
    def change_theme(e):
        page.theme_mode = ft.ThemeMode.DARK if theme_switch.value else ft.ThemeMode.LIGHT
        page.update()

    theme_switch = ft.Switch(label="深色主題模式", value=False, on_change=change_theme)

    # 2. 標頭區塊：標題與頭像圖片
    header = ft.Row(
        controls=[
            ft.Icon(name=ft.icons.PERSON, size=40, color=ft.colors.BLUE),
            ft.Text("我的第一個實用 App", size=24, weight=ft.FontWeight.BOLD),
            theme_switch
        ],
        alignment=ft.MainAxisAlignment.SPACE_BETWEEN
    )

    # 3. 待辦事項清單邏輯
    task_input = ft.TextField(label="輸入新的待辦事項", expand=True)
    tasks_column = ft.Column()  # 用來存放動態新增的清單項目

    def add_task(e):
        if task_input.value:
            # 新增一個包含 Checkbox 的列
            tasks_column.controls.append(
                ft.Checkbox(label=task_input.value, value=False)
            )
            task_input.value = ""  # 清空輸入框
            page.update()  # 刷新畫面

    add_btn = ft.ElevatedButton("新增任務", icon=ft.icons.ADD, on_click=add_task)
    input_row = ft.Row(controls=[task_input, add_btn])

    # 4. 卡片容器區塊 (把 UI 整理得更漂亮)
    card_container = ft.Container(
        content=ft.Column(
            controls=[
                ft.Text("📝 我的待辦清單", size=18, weight=ft.FontWeight.BOLD),
                input_row,
                ft.Divider(),  # 分隔線
                tasks_column
            ]
        ),
        bgcolor=ft.colors.SURFACE_VARIANT,
        padding=20,
        border_radius=10
    )

    # 5. 將所有區塊加入頁面
    page.add(
        header,
        ft.Divider(),
        card_container
    )

ft.app(target=main)
