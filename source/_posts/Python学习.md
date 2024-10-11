---
title: Python学习
tags: 4
abbrlink: e39e2a4e
date: 2022-06-15 16:08:28
---

**Python的学习记录**

<!--more-->

# Python数据结构

## 时间复杂度

```markdown
时间复杂度：是用来估计算法运行时间的一个式子

一般来说，时间复杂度高的算法比复杂度低的算法慢

常见时间复杂度效率排序（由高到低）：
O(1) < O(log2^n) < O(n) < O(n * log2^n) < O(n^2) < O(n^2 * log2^n) < O(n^3)


快速判断算法时间复杂度：
确定问题规模n
循环减半---->>log n
k层关于n的循环---->>n^k


空间复杂度：用来评估算法内存占用大小的式子

算法使用了几个变量：O(1)
算法使用了长度为n的一维列表：O(n)
算法使用了m行n列的二维列表：O(mn)
```

------

## 递归

函数的嵌套调用，在函数中调用它自身

```python
def func1(x):
    if x > 0:
        func1(x-1)
        print(x)

def func2(y):
    if y > 0:
        print(y)
        func2(y-1)
```

递归：计算1+2+...+100的值

```python
def sum_int(n):
    if n > 0:
        return n + sum_int(n-1)
    else:
        return 0
```

递归：汉诺塔问题实例

```python
"""
汉诺塔问题
有n个大小不同的碗，三个盘子，需要将n个碗从一个盘子，保持原样的移动到另一个盘子中去，不改变原本n个碗的摆放顺序
    初始 : n个碗由上到下，由小至大放置于一个盘子之上
    结果 : 在不改变n个碗的摆放顺序的条件下，将所有n个碗"平移"到另一个盘子之上
        要求 : 一次只能移动一个碗，且小碗必须在大碗上面
"""

"""
例:
现有 A B C 三个盘子，a(小碗) b(中碗) c(大碗) 三个碗，
初始: abc摞在A上
过程:
    a -> C | b -> B | a -> B | c -> C | a -> A | b -> C | a -> C
结束: 原在A上的abc，平移至C上，共计如上移动了七次
"""

"""
当有n个碗需要平移时，可以近似将n个碗拆分成 最大的第n个碗 和 其他(n-1)个碗
此时类似于
    第一步 : (n-1)个碗 从A 经过C 至B
    第二步 : 最大的第n个碗 从A 至C
    第三步 : (n-1)个碗 从B 经过A 至C
"""
def hanoi(n, a, b, c):  # 定义的参数的意思是(n个碗， 从盘子a， 经过盘子b， 到达盘子c)

    if n > 0:  # 碗的数量大于0，才有后续递归。即递归终止的条件
        hanoi(n-1, a, c, b)  # 对应上述第一步:从a 经过c 到达b
        print("从 %s 到 %s" % (a, c))
        hanoi(n-1, b, a, c)  # 对应上述第三步:从b 经过a 到达c

hanoi(3, "A", "B", "C")
```

------

# Python算法

**查找:在一些数据元素中，通过一定的方法找出与给定关键字相同的数据元素的过程**

列表查找(线性表查找):从列表中查找指定元素
    输入:列表、待查找元素
    输出:元素下标(未找到元素时一般返回None或-1)

**Python内置列表查找函数: index()**

## 线性查找

```python
# 线性查找(linear_search)：从列表第一个元素开始，顺序进行搜索，直到找到元素或搜索到列表最后一个元素为止。
# 时间复杂度 O(n)
def linear_search(li, val):
    for ind, v in enumerate(li):  # enumerate()是Python的内置函数
        if v == val:
            return ind
    else:
        return None


不用enumerate()函数的代码:

def linear_search(list_str, n, val):
    for i in range(0, n):
        if (list_str[i] == val):
            return i
    return -1
```

## 二分查找

```python
# 二分查找的对象必须为 【！！！ 有序列表 ！！！】
# 二分查找(Binary_search):又叫折半查找，从有序列表的初始候选区li[0:n]开始，通过对待查找的值与候选区中间值的比较，可以使候选区减少一半
# 时间复杂度 O(log n)
def binary_search(li, val):
    left = 0
    right = len(li) - 1
    while left <= right:  # 候选区有值
        mid = (left + right) // 2
        if li[mid] == val:
            return mid
        elif li[mid] > val:
            right = mid - 1  # 待查找的值在mid左侧
        else:  # li(mid) < val 待查找的值在mid右侧
            left = mid + 1
    else:
        return None
```

------

**排序 : 将一组 无序 的记录序列调整为 有序 的记录序列**

列表排序 : 将无序列表变为有序列表
    输入 : 列表
    输出 : 有序列表

升序与降序

**Python内置排序函数 : sort()**

常见排序算法：冒泡排序，选择排序，插入排序
            快速排序，堆排序，归并排序
            希尔排序，计数排序，基数排序

## 冒泡排序

```python
"""
冒泡排序Bubble Sort         时间复杂度O(n^2)

列表每两个相邻的数，如果前面的比后面的大，则交换这两个数

一趟排序完成后，则无序区减少一个数，有序区增加一个数
"""

def bubble_sort(li):
    for i in range(len(li)-1):  # 第i趟
        for j in range(len(li)-i-1):
            if li[j] > li[j+1]:  # j > j+1 为升序排序，改为j < j+1 则为降序排序
                li[j], li[j+1] = li[j+1], li[j]
        print(li)  # 打印每一趟排序
```

## 选择排序

```python
"""
选择排序select sort         时间复杂度O(n^2)

依次遍历列表，每次遍历返回一个最小的元素，再遍历剩下元素找到剩下元素中最小的，循环直到遍历全部最小的元素
"""
def select_sort(li):
    for i in range(len(li)-1):  # i是第几趟
        min_loc = i
        for j in range(i, len(li)):
            if li[j] < li[min_loc]:
                min_loc = j
        li[i], li[min_loc] = li[min_loc], li[i]
        print(li)
```

## 插入排序

```python
"""
插入排序insert sort         时间复杂度O(n^2)

初始有序区只有一个元素，从无序区拿来一个元素，按有序插入原有序区，如此循环

"""

def insert_sort(li):
    for i in range(1, len(li)):  # i 表示拿到的元素的下标
        tmp = li[i]
        j = i - 1  # j 指的是已存入有序区的元素
        while j >= 0 and li[j] > tmp:
            li[j+1] = li[j]
            j -= 1
        li[j+1] = tmp
        print(li)
```

## 快速排序

```python
"""
快速排序quick sort:         时间复杂度O(n * log n)
取一个元素p（第一个元素），使元素p归位
列表被p分成左右两个部分，左边都比p小，右边都比p大。递归完成排序。
"""
def partition(li, left, right):  # left左指针对应0号元素，right右指针对应n-1号元素
    tmp = li[left]

    while left < right:
        while left < right and li[right] >= tmp:  # 从右边找比tmp小的数
            right -= 1           # 往左走一步
        li[left] = li[right]  # 把右边的值写到左边空位上
        # print(li)

        while left < right and li[left] <= tmp:   # 从左边找比tmp大的数
            left += 1            # 往右走一步
        li[right] = li[left]  # 把左边的值写到右边空位上
        # print(li)

    li[left] = tmp  # 把tmp归位
    return left


def quick_sort(li, left, right):
    if left < right:  # 表示列表至少有两个元素
        mid = partition(li, left, right)
        quick_sort(li, left, mid-1)
        quick_sort(li, mid+1, right)
```

## 堆排序

```python
"""
堆排序的过程：         时间复杂度O(n * log n)
        建立堆
        得到堆顶元素，为最大元素
        去掉堆顶，将堆最后一个元素放到堆顶，此时可通过一次调整重新使堆有序
        堆顶元素为第二大元素
        重复"去掉堆顶..."操作，直到堆变空
"""
def sift(li, low, high):
    """
    :param li: 列表
    :param low: 堆的根节点的位置
    :param high: 堆的最后一个元素的位置
    :return:
    """
    i = low  # i最开始指向根节点
    j = 2 * i + 1  # j开始是i的左孩子
    tmp = li[low]  # 把堆顶存起来
    while j <= high:  # 只要j位置有数
        if j + 1 <= high and li[j+1] > li[j]:  # 如果右孩子存在且比左孩子大
            j = j + 1  # 将j指向右孩子
        if li[j] > tmp:
            li[i] = li[j]
            i = j  # 往下看一层
            j = 2 * i + 1
        else:  # tmp更大，把tmp放到i的位置上
            li[i] = tmp  # 把tmp放到某一级领导位置上
            break
    else:
        li[i] = tmp  # 把tmp放到叶子节点上

def heap_sort(li):
    n = len(li)
    for i in range((n-2)//2, -1, -1):
        # i表示建堆的时候调整的部分的根的下标
        sift(li, i, n-1)
    # 建堆完成
    for i in range(n-1, -1, -1):
        li[0], li[i] = li[i], li[0]
        sift(li, 0, i-1)
```

## 归并排序

```python
"""
将列表由mid分为两边low, high
由low->mid，mid+1->high两部分

时间复杂度O(n * log n)
空间复杂度O(n)

分解：将列表越分越小，直至分成一个元素
终止条件：一个元素是有序的
合并：将两个有序列表归并，列表越来越大
"""
def merge(li, low, mid, high):
    i = low
    j = mid + 1
    ltmp = []
    while i <= mid and j <= high:
        if li[i] < li[j]:
            ltmp.append(li[i])
            i += 1
        else:
            ltmp.append(li[j])
            j += 1
    # while执行完后，左半和右半肯定有一部分没有可比较元素
    while i <= mid:
        ltmp.append(li[i])
        i += 1
    while j <= high:
        ltmp.append(li[j])
        j += 1
    li[low:high+1] = ltmp

def merge_sort(li, low, high):
    if low < high:  # 至少有两个元素
        mid = (low + high) // 2
        merge_sort(li, low, mid)
        merge_sort(li, mid+1, high)
        merge(li, low, mid, high)
```

------

# Python实战

## 名片管理系统(基础)

### 实现的功能

1. 无限循环实现系统的连续操作
2. 实现系统基本的增删改查和显示功能

### cards_main.py

```python
import cards_tools
# 无限循环方法
while True:

    # 主界面显示
    cards_tools.show_menu()

    user = input("请选择将要进行的操作:")
    print("您选择的操作是 %s" % user)

    # 系统执行操作1、2、3
    if user in ["1", "2", "3"]:

        # 新增名片功能
        if user == "1":
            cards_tools.new_card()
        # 显示名片功能
        elif user == "2":
            cards_tools.show_card()
        # 查询名片功能
        elif user == "3":
            cards_tools.search_card()

    # 退出系统
    elif user == "0":
        print("欢迎再次使用【名片管理系统】")
        break

    # 用户输入错误3
    else:
        print("输入错误，请重新输入")
```

### cards_tools.py

```python
card_list = []


def show_menu():

    """主界面的显示"""
    print("* " * 15)
    print("    欢迎使用【名片管理系统】")
    print("")
    print("        1.新增名片")
    print("        2.显示名片")
    print("        3.查找名片")
    print("")
    print("        0.退出系统")
    print("* " * 15)


def new_card():

    """新增名片"""
    print("1.新增名片")

    # 1.用户依次输入名片的所有信息
    name = input("请输入用户的姓名:")
    phone = input("请输入用户的电话号码:")
    qq = input("请输入用户的qq号码:")
    email = input("请输入用户的邮箱:")

    # 2.新建一个字典用于保存用户的输入
    card_dict = {"name": name,
                 "phone": phone,
                 "qq": qq,
                 "email": email}

    # 3.将该字典的信息存放到数组上
    card_list.append(card_dict)

    print(card_list)

    # 4.提示用户添加成功
    print("用户 %s 的信息添加成功" % name)


def show_card():

    """显示名片"""
    print("2.显示名片")
    print("- " * 15)

    # 判断是否存在名片信息记录，若没有，提示用户返回
    if len(card_list) == 0:
        print("当前没有任何名片，请使用新增功能先添加名片再进行后续操作！")
        return

    # 打印表头
    for name in card_list:
        print(name, end="\t\t")

    print("")

    for card_dict in card_list:
        print("%s\t\t%s\t\t%s\t\t%s" % (card_dict["name"],
                                        card_dict["phone"],
                                        card_dict["qq"],
                                        card_dict["email"]))
    print("- " * 15)


def search_card():

    """查找名片"""
    print("3.查找名片")

    # 1.提示用户输入要搜索的姓名
    find_name = input("请输入要搜索的用户姓名:")

    # 2.遍历名片列表，查询要搜索的姓名，若没有，需要提示用户
    for card_dict in card_list:
        if card_dict["name"] == find_name:
            print("姓名\t\t电话\t\tQQ\t\t地址")
            print("%s\t\t%s\t\t%s\t\t%s" % (card_dict["name"],
                                            card_dict["phone"],
                                            card_dict["qq"],
                                            card_dict["email"]))
            # 针对找到的名片执行修改和删除操作
            deal_card(card_dict)

            break
    else:
        print("没有找到 %s 的信息" % find_name)


def deal_card(find_dict):

    print(find_dict)

    user_do = input("请选择要执行的操作"
                    "[1]修改  [2]删除  [0]返回上一级")

    if user_do == "1":
        find_dict["name"] = input_card_info(find_dict["name"], "姓名:")
        find_dict["phone"] = input_card_info(find_dict["phone"], "电话:")
        find_dict["qq"] = input_card_info(find_dict["qq"], "QQ:")
        find_dict["email"] = input_card_info(find_dict["email"], "邮箱:")
        print("修改名片成功")

    elif user_do == "2":
        card_list.remove(find_dict)

        print("删除名片成功")


def input_card_info(dict_value, tip_message):

    result_str = input(tip_message)

    if len(result_str) > 0:
        return result_str
    else:
        return dict_value
```

### 控制台效果展示

{% asset_img 名片管理系统效果图1.jpg  %}

{% asset_img 名片管理系统效果图2.jpg  %}

{% asset_img 名片管理系统效果图3.jpg  %}

------

## 飞机大战游戏(pygame)

### **实现的功能**

1. 背景图片的滚动播放
2. 碰撞检测----子弹碰撞飞机，敌机碰撞飞机
3. 键盘←→控制飞机的左右移动
4. random随机数实现敌机的出现数量
5. 代码中可直接修改所有飞机的移动速度

### plane_main.py

```py
# import pygame 飞机精灵导入了pygame的模块
from plane_sprites import *

class PlaneGame(object):
    """飞机大战主游戏"""
    def __init__(self):
        print("...游戏初始化...")

        # 1.创建游戏窗口
        self.screen = pygame.display.set_mode(SCREEN_RECT.size)
        # 2.创建游戏时钟
        self.clock = pygame.time.Clock()
        # 3.调用私有方法，精灵和精灵组的创建
        self.__create_sprites()
        # 4.设置定时器事件 - 创建敌机 1s/架
        pygame.time.set_timer(CREATE_ENEMY_EVENT, 1000)  # 第二个参数以毫秒为单位
        pygame.time.set_timer(HERO_FIRE_EVENT, 500)

    def __create_sprites(self):
        # 创建背景精灵和精灵组
        bg1 = Background()
        bg2 = Background(True)

        self.back_group = pygame.sprite.Group(bg1, bg2)

        # 创建敌机的精灵组
        self.enemy_group = pygame.sprite.Group()

        # 创建英雄的精灵和精灵组
        self.hero = Hero()
        self.hero_group = pygame.sprite.Group(self.hero)

    def start_game(self):
        print("...游戏开始...")

        while True:
            # 1.设置刷新帧率
            self.clock.tick(FRAME_PER_SEC)
            # 2.事件监听
            self.__event_handler()
            # 3.碰撞检测
            self.__check_collide()
            # 4.更新/绘制精灵组
            self.__update_sprites()
            # 5.更新显示
            pygame.display.update()

    def __event_handler(self):
        for event in pygame.event.get():
            # 判断是否退出游戏
            if event.type == pygame.QUIT:
                PlaneGame.__game_over()
            elif event.type == CREATE_ENEMY_EVENT:
                # print("...敌机出场...")
                # 创建敌机精灵
                enemy = Enemy()
                # 将敌机精灵添加到敌机精灵组
                self.enemy_group.add(enemy)
            elif event.type == HERO_FIRE_EVENT:
                self.hero.fire()
            # elif event.type == pygame.KEY_DOWN and event.key == pygame.K_RIGHT:
                # print("向右移动")
        # 使用键盘提供的方法获取键盘按键
        keys_pressed = pygame.key.get_pressed()
        # 判断元组中对应的按键索引值
        if keys_pressed[pygame.K_RIGHT]:
            self.hero.speed = 5
        elif keys_pressed[pygame.K_LEFT]:
            self.hero.speed = -5
        else:
            self.hero.speed = 0

    def __check_collide(self):
        # 1.子弹摧毁敌机
        pygame.sprite.groupcollide(self.hero.bullets, self.enemy_group, True, True)
        # 2.敌机撞毁英雄
        enemies = pygame.sprite.spritecollide(self.hero, self.enemy_group, True)
        # 3.判断列表是否有内容
        if len(enemies) > 0:
            # 让英雄牺牲
            self.hero.kill()
            # 结束游戏
            PlaneGame.__game_over()

    def __update_sprites(self):
        self.back_group.update()
        self.back_group.draw(self.screen)

        self.enemy_group.update()
        self.enemy_group.draw(self.screen)

        self.hero_group.update()
        self.hero_group.draw(self.screen)

        self.hero.bullets.update()
        self.hero.bullets.draw(self.screen)

    @staticmethod
    def __game_over():
        print("游戏结束")
        pygame.quit()
        exit()

if __name__ == '__main__':
    # 创建游戏对象
    game = PlaneGame()
    # 启动游戏
    game.start_game()
```

### plane_sprites.py

```py
import random

import pygame

# 屏幕大小的常量
SCREEN_RECT = pygame.Rect(0, 0, 480, 700)

# 刷新的帧率
FRAME_PER_SEC = 60

# 创建敌机的定时器常量
CREATE_ENEMY_EVENT = pygame.USEREVENT

# 英雄发射子弹事件
HERO_FIRE_EVENT = pygame.USEREVENT + 1

class GameSprite(pygame.sprite.Sprite):
    """飞机大战游戏精灵"""
    def __init__(self, image_name, speed=1):

        # 调用父类的初始化方法
        super().__init__()

        # 定义对象的属性
        self.image = pygame.image.load(image_name)
        self.rect = self.image.get_rect()
        self.speed = speed

    def update(self):
        # 在屏幕的垂直方向上移动
        self.rect.y += self.speed

class Background(GameSprite):
    """游戏背景精灵"""
    def __init__(self, is_alt=False):
        # 1.调用父类方法实现精灵的创建(image/rect/speed)
        super().__init__("./images/background.png")
        # 2.判断是否是交替图像，如果是，需要设置初始位置
        if is_alt:
            self.rect.y = -self.rect.height

    def update(self):

        # 1.调用父类的方法实现
        super().update()

        # 2.判断图像是否移出屏幕，若移出，将图像设置到屏幕的上方
        if self.rect.y >= SCREEN_RECT.height:
            self.rect.y = -self.rect.height

class Enemy(GameSprite):
    """敌机精灵"""
    def __init__(self):
        # 1.调用父类方法，创建敌机精灵，同时指定敌机图片
        super().__init__("./images/enemy1.png")
        # 2.指定敌机的初始随机速度
        self.speed = random.randint(1, 5)
        # 3.指定敌机的初始随机位置
        self.rect.bottom = 0
        max_x = SCREEN_RECT.width - self.rect.width
        self.rect.x = random.randint(0, max_x)

    def update(self):
        # 1.调用父类方法，保持垂直方向的飞行
        super().update()
        # 2.判断是否飞出屏幕，如果是，需要从精灵组删除敌机
        if self.rect.y >= SCREEN_RECT.height:
            # print("飞出屏幕，需要从精灵组删除")

            # kill方法可以将精灵从所有精灵组中移出，精灵就会被自动销毁
            self.kill()

    def __del__(self):
        # print("敌机爆炸 %s" % self.rect)
        pass

class Hero(GameSprite):
    """英雄精灵"""
    def __init__(self):
        # 1.调用父类方法，设置image&speed
        super().__init__("./images/me1.png", 0)
        # 2.设置英雄的初始位置
        self.rect.centerx = SCREEN_RECT.centerx
        self.rect.bottom = SCREEN_RECT.bottom - 120

        # 3.创建子弹的精灵组
        self.bullets = pygame.sprite.Group()

    def update(self):
        # 英雄在水平方向移动
        self.rect.x += self.speed

        # 控制英雄不能离开屏幕
        if self.rect.x < 0:
            self.rect.x = 0
        elif self.rect.right > SCREEN_RECT.right:
            self.rect.right = SCREEN_RECT.right

    def fire(self):
        # print("发射子弹")

        for i in (0, 1, 2):
            # 1.创建子弹精灵
            bullet = Bullet()
            # 2.设置精灵的位置
            bullet.rect.bottom = self.rect.y - i * 20
            bullet.rect.centerx = self.rect.centerx
            # 3.将精灵添加到精灵组
            self.bullets.add(bullet)

class Bullet(GameSprite):
    """子弹精灵"""
    def __init__(self):
        # 调用父类方法，设置子弹图片，设置初始速度
        super().__init__("./images/bullet1.png", -2)

    def update(self):
        # 调用父类方法，让子弹沿垂直方向飞行
        super().update()

        # 判断子弹是否飞出屏幕
        if self.rect.bottom < 0:
            self.kill()

    def __del__(self):
        # print("子弹被销毁")
        pass
```

### 游戏运行效果展示图

{% asset_img 飞机大战.jpg  %}

------

# Python面经

## 列表、元组、字典

1. 列表是动态数组，它们不可变且可以重设长度（改变其内部元素的个数）。
2. 元组是静态数组，它们不可变，且其内部数据一旦创建便无法改变。
3. 元组缓存于Python运行时环境，这意味着我们每次使用元组时无须访问内核去分配内存。

**列表元组字典的一些区别：**

1、列表list：有序，可变。

2、元组tuple：有序，不可变。

3、字典dict：无序，字典的key是不可变的，值是可变的；在python中字符串，数字，元组都是不可变的对象。

4、序列：列表，元组和字符串都属于序列；即成员有序排列，可以通过下标访问，可以进行切片操作。字符串（普通字符串和unicode字符串）

5、集合：没有顺序的简单对象的聚集。可以检查成员是否存在，是否是另一个集合的子集，得到两个集合的交集等等

6、复制一个列表或者类似的序列或者其他复杂对象，那么必须使用切片操作符来去的拷贝。当你创建一个对象并将它赋值给一个变量的时候，这个变量仅仅引用那个对象，而不是表示这个对象本身！也就是说，变量名指向你计算机中存储那个对象的内存。这被称作名称到对象的绑定。

## Python的异常机制

### 1、什么是异常？

异常即是一个事件，该事件会在程序执行过程中发生，影响了程序的正常执行。
 一般情况下，在Python无法正常处理程序时就会发生一个异常。
 异常是Python对象，表示一个错误。
 当Python脚本发生异常时我们需要捕获处理它，否则程序会终止执行。

### 2、try…except…else结构

如果try块中没有抛出异常，则执行else块，如果try中抛出异常，则执行except模块
 except：捕获所有的异常

不带任何异常类型使用except，如下实例：

```
try:
    正常的操作
   ......................
except:
    发生异常，执行这块代码
   ......................
else:
    如果没有异常执行这块代码

```

使用相同的except语句来处理多个异常信息，如下所示：

```
try:
    正常的操作
   ......................
except(Exception1[, Exception2[,...ExceptionN]]):
   发生以上多个异常中的一个，执行这块代码
   ......................
else:
    如果没有异常执行这块代码

```

### 3、try…except…else…finally

finally块无论是否发生异常都会执行，能常用来解释try块中申请的的资源

### 4、python中常见的异常类型

Exception：常规错误的基类
ZeroDivisionError：除(或取模)零 (所有数据类型)
AssertionError：断言语句失败
AttributeError：对象没有这个属性
IOError：输入/输出操作失败
ImportError：导入模块/对象失败
IndexError：序列中没有此索引(index)
KeyError：映射中没有这个键
NameError：未声明/初始化对象 (没有属性)
SyntaxError：Python 语法错误
IndentationError：缩进错误
SystemError：一般的解释器系统错误
TypeError：对类型无效的操作
ValueError：传入无效的参数

### python异常代码块

```py
    try:
        # func   # 可能会抛出异常的代码
    except (Exception1, Exception2) as e:  # 可以捕获多个异常并处理
        # 异常处理的代码
    else:
        # pass  # 异常没有发生的时候代码逻辑
    finally:
        pass     # 无论异常有没有发生都会执行的代码，一般处理资源的关闭和释放

```

------

## Python中的GIL

### 定义

全局解释器锁 GIL，英文名称为 Global Interpreter Lock，它是解释器中一种线程同步的方式。

 

对于每一个解释器进程都具有一个 GIL ，它的直接作用是限制单个解释器进程中多线程的并行执行，使得即使在多核处理器上对于单个解释器进程来说，在同一时刻运行的线程仅限一个。 对于 Python 来讲，GIL 并不是它语言本身的特性，而是 CPython 解释器的实现特性。

 

Python 代码被编译后的字节码会在解释器中执行，在执行过程中，存在于 CPython 解释器中的 GIL 会致使在同一时刻只有一个线程可以执行字节码。 GIL 的存在引起的最直接的问题便是：在一个解释器进程中通过多线程的方式无法利用多核处理器来实现真正的并行。

 

因此，Python的多线程是伪多线程，无法利用多核资源，同一个时刻只有一个线程在真正的运行。

 

GIL的限制了程序的多核执行

 

  同一个时间只能有一个线程执行字节码

  CPU密集程序难以利用多核优势

  IO期间会释放GIL，对IO密集程序影响不大

 

面对GIL的存在，我们有可以有多个方法帮助我们提升性能

在 IO 密集型任务下，我们可以使用多线程或者协程来完成。

可以选择更换 Jython 等没有 GIL 的解释器，但并不推荐更换解释器，因为会错过众多 C 语言模块中的有用特性。

CPU密集可以使用多进程+进程池。

将计算密集型任务转移到 Python 的 C / C++ 扩展模块中完成

### 为什么有了GIL还要关注线程安全

GIL 保证的是每一条字节码在执行过程中的独占性，即每一条字节码的执行都是原子性的。GIL 具有释放机制，所以 GIL 并不会保证字节码在执行过程中线程不会进行切换，即在多个字节码之间，线程具有切换的可能性。

我们可以用python的dis模块去查看a += 1执行的字节码，发现需要有多个字节码去完成，线程具有切换的可能性，所以它是非线程安全的。

 一个操作如果是一个字节码指令可以完成就是原子的，非原子操作不是线程安全的，原子的是可以保证线程安全的。

 GIL 和线程互斥锁的粒度是不同的，GIL 是 Python 解释器级别的互斥，保证的是解释器级别共享资源的一致性，而线程互斥锁则是代码级（或用户级）的互斥，保证的是 Python 程序级别共享数据的一致性，所以我们仍需要线程互斥锁及其他线程同步方式来保证数据一致。

## 迭代器和生成器

{% asset_img 迭代器和生成器.jpg  %}

容器（container）

container 可以理解为把多个元素组织在一起的数据结构，container 中的元素可以逐个地迭代获取，可以用 in, not in 关键字判断元素是否包含在容器中。比如Python中常见的container对象有list，deque，set

 

可迭代对象(iterables)

大部分的 container 都是可迭代对象，比如 list or set 都是可迭代对象，可以说只要是可以返回一个迭代器的都可以称作可迭代对象。

 

迭代器（iterator）

python中的容器有许多，比如列表、元组、字典、集合等，对于容器，可以很直观地想象成多个元素在一起的单元，所有的容器都是可迭代的（iterable）。

 

我们通常使用for in 语句对可迭代的对象进行枚举，其底层机制在于：

而可迭代对象，通过 iter() 函数返回一个迭代器（iterator），迭代器提供了一个 next 的方法。调用用这个方法后，你要么得到这个容器的下一个对象，要么得到一个StopIteration 的错误。

 

举个例子：

```markdown
  \>>> x = [1, 2, 3]

  \>>> # Get the iterator

  \>>> y = iter(items) # Invokes items.__iter__()

  \>>> # Run the iterator

  \>>> next(y) # Invokes it.__next__()

  1

  \>>> next(y)

  2

  \>>> next(y)

  3

  \>>> type(x)

  <class 'list'>

  \>>> type(y)

  <class 'list_iterator'>

  \>>> next(y)

  Traceback (most recent call last):

​    File "<stdin>", line 1, in <module>

  StopIteration

  \>>>
```

 

上面的例子中，x=[1,2,3]是可迭代对象，这里也叫容器。y=iter(x)则是迭代器，且实现了__iter__和__next__方法。

 

它们之间的关系如下图所示：

{% asset_img 迭代器实例.jpg  %}

可见通过 iter 方法后就是迭代器。它是一个带状态的对象，调用 next 方法的时候返回容器中的下一个值，可以说任何实现了iter和 next 方法的对象都是迭代器，iter返回迭代器自身，next 返回容器中的下一个值，如果容器中没有更多元素了，则抛异常。

 

迭代器就像一个懒加载的工厂，等到有人需要的时候才给它生成值返回，没调用的时候就处于休眠状态等待下一次调用。

 

生成器（generator）

 

生成器(generator)可以简单理解为懒人版本的迭代器。

 

它相比于迭代器的优势是，生成器并不会像迭代器一样占用大量内存。比如声明一个迭代器：[i for i in range(100000000)]就可以声明一个包含一亿个元素的列表，每个元素在生成后都会保存到内存中。但实际上我们也许并不需要保存那么多东西，只希望在你用 next() 函数的时候，才会生成下一个变量，因此生成器应运而生，在python中的写法为(i for i in range(100000000))

 

此外，生成器还可以有别的形式，比如生成器函数，通过yield关键字，把结果返回到next()方法中，举个例子：

```py
 def frange(start, stop, increment):
	x = start
	while x < stop:
		yield x
    	x += increment
 for n in frange(0, 2, 0.5):
    print(n)

  0
  0.5
  1.0
  1.5
```

相比于迭代器，生成器具有以下优点：

1. 减少内存
2. 延迟计算
3. 有效提高代码可读性

## python深拷贝和浅拷贝

- 浅拷贝，指的是重新分配一块内存，创建一个新的对象，但**里面的元素是原对象中各个子对象的引用**。
- 深拷贝，是指重新分配一块内存，创建一个新的对象，并且将原对象中的元素，以递归的方式，通过创建新的子对象拷贝到新对象中。因此，**新对象和原对象没有任何关联**。

### 浅拷贝

- 使用数据类型**本身的构造器**
- 对于**可变的序列**，还可以通过切片操作符 : 来完成浅拷贝
- Python 还提供了对应的函数 **copy.copy()** 函数，适用于任何数据类型

对于元组，使用 tuple() 或者切片操作符 ‘:’ 不会创建一份浅拷贝，相反它会返回一个指向相同元组的引用：

使用 tuple() 或者切片操作符 ‘:’ **不会创建一份浅拷贝**，因为它开辟新的内存存储的是**原对象的引用**，而没有创建新的对象来存储原对象的子对象的引用，所以不是浅拷贝。相反它会返回一个**指向相同元组的引用。**

对字符串使用 str() 或者切片操作符 ‘:’，原理和 元组相同。

也就是说，对字符串和元组使用 copy（）、[:]、本身的构造器完成的复制，都只是开辟了内存存储原对象的引用，而不是存储原对象的子对象的引用。

### 深拷贝

Python 中以 copy.deepcopy() 来实现对象的深度拷贝

### 两者的优缺点对比

（1）深拷贝拷贝程度高，将原数据复制到新的内存空间中。改变拷贝后的内容不影响原数据内容。但是深拷贝耗时长，且占用内存空间。
（2）浅拷贝拷贝程度低，只复制原数据的地址。其实是将副本的地址指向原数据地址。修改副本内容，是通过当前地址指向原数据地址，去修改。所以修改副本内容会影响到原数据内容。但是浅拷贝耗时短，占用内存空间少
