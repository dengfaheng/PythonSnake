# 更多精彩尽在微信公众号【程序猿声】
![微信公众号](http://upload-images.jianshu.io/upload_images/10386940-20b6183b3271dbae.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 10分钟教你用python打造贪吃蛇超详细教程

在家闲着没妹子约, 刚好最近又学了一下python，听说pygame挺好玩的。今天就在家研究一下， 弄了个贪吃蛇出来。希望没女朋友的大家喜欢~~~

先看程序效果：

![image](http://upload-images.jianshu.io/upload_images/10386940-9b5ff1eb5c5d20db.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/10386940-176d131ed49bd88e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 01 整体框架
平台：pycharm

关于pygame的安装这里就不在赘述，大家自行上网找合适自己的版本的安装即可。关于pygame模块知识会穿插在下面代码中介绍，用到什么就介绍什么。这里就不统一介绍了。

整个程序由于是调用了大量的pygame里面的库函数，所以也非常简单（卧槽你这不是调包侠嘛）。也就200多行代码。基于整体怎么设计的呢？看下面的图：

![image](http://upload-images.jianshu.io/upload_images/10386940-15920cb4033245f7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于程序没有多么复杂，就直接用面向过程的思路写了。毕竟这么小的程序没必要整一大堆class来为难自己对吧。

程序整体代码框架：
![image](http://upload-images.jianshu.io/upload_images/10386940-0022e8f1b0109e2a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

pycharm里面一堆波浪线也是很无奈。

## 02 main主函数-开始工作
此函数也非常简单。主要承担一些游戏窗口的初始化工作，以及调用相关函数运行游戏。代码如下：

```python
#主函数
def main():
	pygame.init() # 模块初始化
	snake_speed_clock = pygame.time.Clock() # 创建Pygame时钟对象
	screen = pygame.display.set_mode((windows_width, windows_height)) #
	screen.fill(white)

	pygame.display.set_caption("Python 贪吃蛇小游戏") #设置标题
	show_start_info(screen)               #欢迎信息
	while True:
		running_game(screen, snake_speed_clock)
		show_gameover_info(screen)

```

基于以上代码，咱们来做几点讲解：
- **pygame.time.Clock()**
    控制帧速率。pygame.time.Clock()会控制每个循环多长时间运行一次。这就好比，有个定时器在控制着时间进程，一到时间就告诉CPU：
    现在该开始循环了！
    现在该开始循环了！

    使用pygame时钟之前，必须先创建Clock对象的一个实例，这与创建其他类的实例完全相同。Clock= Pygame.time.Clock()。然后在主循环体中，只需要告诉时钟多久“提醒”一次-------也就是说，循环应该多长时间运行一次：clock.tick(60)。

    传入clock.tick()的数不是一个毫秒数。这是每秒内循环要运行的次数，所以这个循环应当每秒运行60次，在这里我只是说应当运行，因为循环只能按计算机能够保证的速度运行，每秒60个循环（或帧）时，每个循环需要1000/60=16.66ms（大约17ms）如果循环中的代码运行时间超过17ms，在clock指出下一次循环时当前循环将无法完成。

    再说通俗一点，就是我们游戏的fps嘛。每秒多少帧这样。至于后面在哪clock.tick()，下面会讲。
    详细可参考这篇文章：http://eyehere.net/2011/python-pygame-novice-professional-8/

- **pygame.display.set_mode((windows_width, windows_height))**

    生成windows窗口，pygame.display.set_mode(resolution=(0,0),flags=0,depth=0)。返回的是一个surface对象(surface对象是用于表示图像的图像，只要指定尺寸，就可以利用)，resolution可以控制生成windows窗口的大小，flags代表的是扩展选项，depath不推荐设置。

    flags标志位控制你想要什么样的显示屏，主要有下面几个，这几个量相当于是全局的常量，使用的时候可以from pygame.locals import *导入：
    - pygame.FULLSCREEN，控制全屏,0或者1来控制
    - pygame.HWSURFACE 控制是否进行硬件加速
    - pygame.RESIZABLE 控制窗口是否可以调节大小

- **screen.fill(white）**
pygame.surface.fill(color)。对surface对象填充某一种颜色，在这里表现为窗口背景颜色的填充。

以上讲完，然后就是运行我们游戏三个函数了。
- show_start_info(screen)
显示欢迎信息，最终效果表现为：

    ![image](http://upload-images.jianshu.io/upload_images/10386940-d3c6b2125adf326a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    当然，怎么实现，待会说。

接着死循环。因为我们的游戏设置是，当GameOver以后，我们可以按任意键重新开始游戏，或者退出。因此最后不断循环判断用户是否想重新开始游戏，就这样而已。

- **游戏主体running_game(screen, snake_speed_clock)**
贪吃蛇运行的主体函数。整个程序的精髓所在。

- **show_gameover_info(screen)**
贪吃蛇死了，显示GameOver，表现为：

    ![image](http://upload-images.jianshu.io/upload_images/10386940-721417c42a072173.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    怎么实现，下面说。

## 03 show_start_info()欢迎进入游戏
先贴代码，待会讲解。
```python

#开始信息显示
def show_start_info(screen):
	font = pygame.font.Font('myfont.ttf', 40)
	tip = font.render('按任意键开始游戏~~~', True, (65, 105, 225))
	gamestart = pygame.image.load('gamestart.png')
	screen.blit(gamestart, (140, 30))
	screen.blit(tip, (240, 550))
	pygame.display.update()

	while True:  #键盘监听事件
		for event in pygame.event.get():  # event handling loop
			if event.type == QUIT:
				terminate()     #终止程序
			elif event.type == KEYDOWN:
				if (event.key == K_ESCAPE):  #终止程序
					terminate() #终止程序
				else:
					return #结束此函数, 开始游戏

```
- 字体显示
    先创建一个Font对象，用自己的字体。有了Font对象以后， 就可以用render方法来写字了，然后通过blit方法blit到屏幕上。
- 图像加载
    用 pygame.image.load()加载图像获得对象，在用blit方法刷到屏幕上。**做完以上事件以后，记得要update一下刷新一下屏幕。**
- 监听键盘
    按任意键继续或者ESC退出……

## 04 running_game-让我们开始游戏吧
running_game(screen, snake_speed_clock)是游戏主要功能，在这里给大家慢慢讲解。先贴代码：
```python
#游戏运行主体
def running_game(screen,snake_speed_clock):
	startx = random.randint(3, map_width - 8) #开始位置
	starty = random.randint(3, map_height - 8)
	snake_coords = [{'x': startx, 'y': starty},  #初始贪吃蛇
                  {'x': startx - 1, 'y': starty},
                  {'x': startx - 2, 'y': starty}]

	direction = RIGHT       #  开始时向右移动

	food = get_random_location()     #实物随机位置

	while True:
		for event in pygame.event.get():
			if event.type == QUIT:
				terminate()
			elif event.type == KEYDOWN:
				if (event.key == K_LEFT or event.key == K_a) and direction != RIGHT:
					direction = LEFT
				elif (event.key == K_RIGHT or event.key == K_d) and direction != LEFT:
					direction = RIGHT
				elif (event.key == K_UP or event.key == K_w) and direction != DOWN:
					direction = UP
				elif (event.key == K_DOWN or event.key == K_s) and direction != UP:
					direction = DOWN
				elif event.key == K_ESCAPE:
					terminate()

		move_snake(direction, snake_coords) #移动蛇

		ret = snake_is_alive(snake_coords)
		if not ret:
			break #蛇跪了. 游戏结束
		snake_is_eat_food(snake_coords, food) #判断蛇是否吃到食物

		screen.fill(BG_COLOR)
		#draw_grid(screen)
		draw_snake(screen, snake_coords)
		draw_food(screen, food)
		draw_score(screen, len(snake_coords) - 3)
		pygame.display.update()
		snake_speed_clock.tick(snake_speed) #控制fps
```
- 关于贪吃蛇
    这里我们采用一个元组存储贪吃蛇身体各个部分的坐标(一条贪吃蛇不是由很多节组成的嘛)。最后再写个方法根据元组坐标把贪吃蛇画出来就行。
- 关于食物
    同样做法。存坐标，最后画出来。
- 关于移动
    监听键盘，根据用户按键，用direction变量记录移动方向。然后更新贪吃蛇元组里面的坐标(其实每次移动只用更新头尾就行)。最后统一画出来。移动做法具体是，我们把每次头部移动的新坐标插入贪吃蛇元组，然后删掉尾部一节(注意，删除尾部我们放在了另外一个函数里做)。
    ```python
    #移动贪吃蛇
    def move_snake(direction, snake_coords):
        if direction == UP:
            newHead = {'x': snake_coords[HEAD]['x'], 'y': snake_coords[HEAD]['y'] - 1}
        elif direction == DOWN:
            newHead = {'x': snake_coords[HEAD]['x'], 'y': snake_coords[HEAD]['y'] + 1}
        elif direction == LEFT:
            newHead = {'x': snake_coords[HEAD]['x'] - 1, 'y': snake_coords[HEAD]['y']}
        elif direction == RIGHT:
            newHead = {'x': snake_coords[HEAD]['x'] + 1, 'y': snake_coords[HEAD]['y']}

        snake_coords.insert(0, newHead)

    ```

- 开始阶段
    先把贪吃蛇和食物的坐标随机生成，贪吃蛇一开始3节长，先设置向右移动。
- 移动我们的贪吃蛇
    监听键盘，用户按下键盘只是改变direction的值，再用move_snake(direction, snake_coords)函数更新贪吃蛇坐标。如果不按，那direction值一直不变，贪吃蛇就一直向前走。
- 相关判断
    要判断贪吃蛇是否挂了，表现为：
    - 头坐标超出地图范围
    - 头坐标等于身体某节坐标
    ```python
        #判断蛇死了没
    def snake_is_alive(snake_coords):
        tag = True
        if snake_coords[HEAD]['x'] == -1 or snake_coords[HEAD]['x'] == map_width or snake_coords[HEAD]['y'] == -1 or \
                snake_coords[HEAD]['y'] == map_height:
            tag = False # 蛇碰壁啦
        for snake_body in snake_coords[1:]:
            if snake_body['x'] == snake_coords[HEAD]['x'] and snake_body['y'] == snake_coords[HEAD]['y']:
                tag = False # 蛇碰到自己身体啦
        return tag
    ```

    判断贪吃蛇是否吃到食物，表现为：
    - 头坐标等于食物坐标，那么吃到食物。这时候注意，我们就不用删尾部一节了，因为吃到食物变长了嘛。
    - 如果没有吃到食物，那么是正常移动，删掉尾部一节坐标。
    ```python
        #判断贪吃蛇是否吃到食物
    def snake_is_eat_food(snake_coords, food):  #如果是列表或字典，那么函数内修改参数内容，就会影响到函数体外的对象。
        if snake_coords[HEAD]['x'] == food['x'] and snake_coords[HEAD]['y'] == food['y']:
            food['x'] = random.randint(0, map_width - 1)
            food['y'] = random.randint(0, map_height - 1) # 实物位置重新设置
        else:
            del snake_coords[-1]  # 如果没有吃到实物, 就向前移动, 那么尾部一格删掉
    ```

- 画出我们的游戏
最后调用相关函数，讲我们的地图，贪吃蛇，食物等等统统画出来。

## 05 draw_snake-画出我们的贪吃蛇
直接看代码：
```python
def draw_snake(screen, snake_coords):
	for coord in snake_coords:
		x = coord['x'] * cell_size
		y = coord['y'] * cell_size
		wormSegmentRect = pygame.Rect(x, y, cell_size, cell_size)
		pygame.draw.rect(screen, dark_blue, wormSegmentRect)
		wormInnerSegmentRect = pygame.Rect(                #蛇身子里面的第二层亮蓝色色
			x + 4, y + 4, cell_size - 8, cell_size - 8)
		pygame.draw.rect(screen, blue, wormInnerSegmentRect)
```
代码很easy，主要是获取相关坐标，最后调用pygame.draw.rect将身体各个部分画出来即可。不过为了美观，我们选择再在里面画一层不同颜色的，表现为：

![image](http://upload-images.jianshu.io/upload_images/10386940-0038e7f31a092ee6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 06 draw_food-画出我们的食物
```python
#将食物画出来
def draw_food(screen, food):
	x = food['x'] * cell_size
	y = food['y'] * cell_size
	appleRect = pygame.Rect(x, y, cell_size, cell_size)
	pygame.draw.rect(screen, Red, appleRect)
```
更简单的代码了，获取位置，画矩形。


## 07 draw_score-画出我们的成绩
```python
#画成绩
def draw_score(screen,score):
	font = pygame.font.Font('myfont.ttf', 30)
	scoreSurf = font.render('得分: %s' % score, True, Green)
	scoreRect = scoreSurf.get_rect()
	scoreRect.topleft = (windows_width - 120, 10)
	screen.blit(scoreSurf, scoreRect)
```
画成绩也比较简单。获得Font对象以后，render写字，最后设置位置，在屏幕上blit出来。

## 08 完整代码
整个程序大体如上，其他细枝末节直接看源代码吧。

欲获取代码，请关注我们的微信公众号【程序猿声】，在后台回复：**python贪吃蛇**。python和贪吃蛇之间不要有空格，不要有空格，不要有空格！！！即可获取。

![微信公众号](http://upload-images.jianshu.io/upload_images/10386940-546ac15b9d7add56.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

推荐文章：[10分钟教你用Python做个打飞机小游戏超详细教程](https://www.jianshu.com/p/3bdea5b64269)
推荐文章：[10分钟教你用python下载和拼接微信好友头像图片](https://www.jianshu.com/p/31b705887fa1)