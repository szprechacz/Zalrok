# Zalrok
Snake clone
import time
import pygame
import random

pygame.init()

clock = pygame.time.Clock()


def main():

    width = 600

    rows = 20

    win = pygame.display.set_mode((width, width + 60))

    pygame.display.set_caption("Zalrok")

    groundPicture = pygame.image.load('pictures/ground.jpg')
    reptileSkin = pygame.image.load('pictures/skin.jpg')
    reptileHead = pygame.image.load('pictures/head.png')
    reptileTail = pygame.image.load('pictures/tail.png')
    egg = pygame.image.load('pictures/egg.png')
    icon = pygame.image.load('pictures/icon.ico')
    pygame.display.set_icon(icon)
    mediumFont = pygame.font.SysFont('Georgia', 27, True)
    largeFont = pygame.font.SysFont('Georgia', 40, True)

    class Player(object):

        global reptileHead0, reptileSkin0, reptileTail0

        reptileHead0 = pygame.transform.rotate(reptileHead, 0)
        reptileSkin0 = pygame.transform.rotate(reptileSkin, 0)
        reptileTail0 = pygame.transform.rotate(reptileTail, 180)

        def __init__(self, x, y, direction, width, radius, length):

            self.x = x
            self.y = y
            self.direction = direction
            self.width = width
            self.radius = radius
            self.turns = [[300, 270, "NONE"], [270, 270, "NONE"]]
            self.length = length

        def drawBody(self, win):

            global reptileTail0, reptileSkinUp, reptileSkinDown, reptileSkinRight, reptileSkinLeft

            reptileSkinUp = pygame.transform.rotate(reptileSkin, 180)
            reptileSkinDown = pygame.transform.rotate(reptileSkin, 0)
            reptileSkinRight = pygame.transform.rotate(reptileSkin, 90)
            reptileSkinLeft = pygame.transform.rotate(reptileSkin, 270)

            for i in range(0, Snake.length):
                if Snake.turns[i + 1][2] == 'RIGHT':
                    win.blit(reptileSkinRight,
                             [Snake.turns[i + 1][0], Snake.turns[i + 1][1], self.width, self.width])

                if Snake.turns[i + 1][2] == 'LEFT':
                    win.blit(reptileSkinLeft,
                             [Snake.turns[i + 1][0], Snake.turns[i + 1][1], self.width, self.width])

                if Snake.turns[i + 1][2] == 'UP':
                    win.blit(reptileSkinUp,
                             [Snake.turns[i + 1][0], Snake.turns[i + 1][1], self.width, self.width])

                if Snake.turns[i + 1][2] == 'DOWN':
                    win.blit(reptileSkinDown,
                             [Snake.turns[i + 1][0], Snake.turns[i + 1][1], self.width, self.width])

            if Snake.turns[1][2] == 'UP':
                reptileTail0 = pygame.transform.rotate(reptileTail, 90)
                win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

            elif Snake.turns[1][2] == 'DOWN':
                reptileTail0 = pygame.transform.rotate(reptileTail, 270)
                win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

            elif Snake.turns[1][2] == 'RIGHT':
                reptileTail0 = pygame.transform.rotate(reptileTail, 0)
                win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

            elif Snake.turns[1][2] == 'LEFT':
                reptileTail0 = pygame.transform.rotate(reptileTail, 180)
                win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

            elif Snake.turns[0][2] == 'NONE':

                win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

            win.blit(reptileHead0, [Snake.x, Snake.y, self.width, self.width])

        def move(self):

            global changeX
            global changeY
            global reptileHead0

            keys = pygame.key.get_pressed()

            if (keys[pygame.K_RIGHT] or keys[pygame.K_d]) and Snake.direction != "LEFT":
                Snake.direction = "RIGHT"
                changeX = - width / rows
                changeY = 0

            elif (keys[pygame.K_LEFT] or keys[pygame.K_a]) and Snake.direction != "RIGHT":
                Snake.direction = "LEFT"
                changeX = + width / rows
                changeY = 0

            elif (keys[pygame.K_DOWN] or keys[pygame.K_s]) and Snake.direction != "UP":
                Snake.direction = "DOWN"
                changeX = 0
                changeY = - width / rows

            elif (keys[pygame.K_UP] or keys[pygame.K_w]) and Snake.direction != "DOWN":
                Snake.direction = "UP"
                changeX = 0
                changeY = + width / rows

# SNAKE MOVES ALL THE TIME

            if Snake.direction == "UP":
                Snake.y -= width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 270)

            if Snake.direction == "DOWN":
                Snake.y += width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 90)

            if Snake.direction == "RIGHT":
                Snake.x += width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 180)

            if Snake.direction == "LEFT":
                Snake.x -= width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 0)

# SNAKE CAN CROSS THE WINDOW:

            if Snake.x < 0 and Snake.direction == "LEFT":
                Snake.x = width - width / rows

            if Snake.x > width - width / rows and Snake.direction == "RIGHT":
                Snake.x = 0

            if Snake.y < 0 and Snake.direction == "UP":
                Snake.y = width - width / rows

            if Snake.y > width - width / rows and Snake.direction == "DOWN":
                Snake.y = 0

            self.turns.append([self.x, self.y, self.direction])

        def addBody(length):

            Snake.length += 1

    class Snack(object):

        def __init__(self, x, y, width):
            self.x = x
            self.y = y
            self.width = width

# PROVIDES THAT COOKIE WILL NOT APPEAR INSIDE THE SNAKE

        def newSnackDraw(self, win):

            xySnakeTurns = [x[:-1] for x in Snake.turns]

            area = []

            for p in range(int(rows) ):

                for p1 in range(int(rows) ):
                    area.append([p * width / rows, p1 * width / rows])

            i = 0

            while i < len(Snake.turns):

                if xySnakeTurns[i] in area:

                    area.remove( xySnakeTurns[i] )
                i += 1

            np = random.randrange( 0, len(area), 1)
            self.x = area[np][0]
            self.y = area[np][1]
            win.blit(egg, (self.x, self.y, width / rows, width / rows))

        def draw(self, win):

            win.blit(egg, (self.x, self.y, width / rows, width / rows))

    def drawGrid():
        sizeBtwn = width // rows
        x = 0
        y = 0

        for i in range(rows):
            x = x + sizeBtwn
            y = y + sizeBtwn

            pygame.draw.line(win, (255, 255, 255), (x, 0), (x, width))
            pygame.draw.line(win, (255, 255, 255), (0, y), (width, y))

    def updateFile():

        f = open('scores.txt', 'r')
        file = f.readlines()
        bestScore = int(file[0])
        bestTime = float(file[1])

        if bestScore < int(Snake.length):

            file = open('scores.txt', 'w')
            file.writelines(str(Snake.length) + '\n' + str(resultTime))
            file.close()
            text00 = largeFont.render("BRAVO! You are THE BEST!!!", 1, (255, 255, 17))
            text01 = largeFont.render("BRAVO! You are THE BEST!!!", 1, (0, 0, 0))
            win.blit(text00, (20, 0))
            win.blit(text01, (18, 2))
            pygame.display.update()

            return [Snake.length, resultTime]

        if bestScore == int(Snake.length) and bestTime > float(resultTime):

            file = open('scores.txt', 'w')
            file.writelines(str(Snake.length) + '\n' + str(resultTime))
            file.close()
            text00 = largeFont.render("BRAWO! You are THE BEST!", 1, (255, 255, 17))
            text01 = largeFont.render("BRAWO! You are THE BEST!", 1, (0, 0, 0))
            win.blit(text00, (20, 0))
            win.blit(text01, (18, 2))
            pygame.display.update()

            return [Snake.length, resultTime]

        else:

            f.close()
            return [bestScore, bestTime]

    def endTitles():

        bestResult = updateFile()
        print(bestResult)
        GameOver = True
        while GameOver:


            for event in pygame.event.get():

                if event.type == pygame.QUIT:
                    pygame.quit()
                    quit()

                text10 = largeFont.render("Best result points: " + str(bestResult[0]), 1, (255, 255, 17))
                text11 = largeFont.render("Best result points: " + str(bestResult[0]), 1, (0, 0, 0))
                text20 = largeFont.render("B. r. time: " + str(bestResult[1]) + ' s', 1, (255, 255, 17))
                text21 = largeFont.render("B. r. time: " + str(bestResult[1]) + ' s', 1, (0, 0, 0))
                text30 = largeFont.render("Play again: press b", 1, (255, 255, 17))
                text31 = largeFont.render("Play again: press b", 1, (0, 0, 0))
                text40 = largeFont.render("Exit: press ESC ", 1, (255, 255, 17))
                text41 = largeFont.render("Exit: press ESC ", 1, (0, 0, 0))

                win.blit(text10, (20, 60))
                win.blit(text11, (18, 62))
                win.blit(text20, (20, 120))
                win.blit(text21, (18, 122))
                win.blit(text30, (20, 180))
                win.blit(text31, (18, 182))
                win.blit(text40, (20, 240))
                win.blit(text41, (18, 242))

                pygame.display.update()

                if event.type == pygame.KEYDOWN:

                    if event.key == pygame.K_ESCAPE:
                        pygame.quit()
                        quit()
                    if event.key == pygame.K_b:
                        main()


    Snake = Player( 270, 270,
                   width / rows, width / rows / 10, 0, 0 )
    Cookie = Snack( 30, 270,
                   width / rows )
    beginList = []

    def firstMessage():

        text00 = mediumFont.render(" Hey! Do want to feed our hungry snake - Zalrok? ", 1, (255, 255, 17))
        text01 = mediumFont.render(" Hey! Do want to feed our hungry snake - Zalrok? ", 1, (0, 0, 0))
        text10 = mediumFont.render(" Press one of the directional keys to start the feast ", 1, (255, 255, 17))
        text11 = mediumFont.render(" Press one of the directional keys to start the feast ", 1, (0, 0, 0))
        text20 = mediumFont.render(" Directional keys: 'a' 'w' 's' 'd' and arrows ", 1, (255, 255, 17))
        text21 = mediumFont.render(" Directional keys: 'a' 'w' 's' 'd' and arrows ", 1, (0, 0, 0))
        text30 = mediumFont.render(" And yes, Zalrok cannot move backwards ;) ", 1, (255, 255, 17))
        text31 = mediumFont.render(" And yes, Zalrok cannot move backwards ;) ", 1, (0, 0, 0))
        text40 = largeFont.render(" HAVE FUN! ", 1, (255, 255, 17))
        text41 = largeFont.render(" HAVE FUN! ", 1, (0, 0, 0))

        win.blit(text00, (8, 40))
        win.blit(text01, (10, 41))
        win.blit(text10, (8, 100))
        win.blit(text11, (10, 101))
        win.blit(text20, (8, 160))
        win.blit(text21, (10, 161))
        win.blit(text30, (8, 220))
        win.blit(text31, (10, 221))
        win.blit(text40, (168, 330))
        win.blit(text41, (170, 331))


    def showClock():

        global resultTime

        beginList.append(round(time.time() * 1000))

        if len(beginList) >= 2:

            beginList.pop(1)

        end = round(time.time() * 1000)
        resultTime = str((end - beginList[0]) / 1000)

        text = mediumFont.render("Score: " + str(Snake.length)
                                 + " Time: " + str(resultTime) + " s", 1, (255, 255, 17))

        win.blit(text, (10, 610))

    def redrawGameWindow():

        win.fill((100, 100, 0))
        win.blit(groundPicture, (0, 0))

        Snake.drawBody(win)
        Cookie.draw(win)

        keys = pygame.key.get_pressed()

        if Snake.turns[-1][2] == "NONE":

            firstMessage()

        if Snake.turns[-1][2] != "NONE" or keys[pygame.K_LEFT] or keys[pygame.K_a] or keys[pygame.K_UP] or keys[
            pygame.K_w] or keys[pygame.K_DOWN] or keys[pygame.K_s]:

            showClock()

    def runGame():

        run = True

        while run:

            pygame.display.update()
            print(Snake.turns)
            print(Snake.length)

            keys = pygame.key.get_pressed()

            if Snake.turns[-1][2] != "NONE" or keys[pygame.K_LEFT] or keys[pygame.K_a] or keys[pygame.K_UP] or keys[pygame.K_w] or keys[pygame.K_DOWN] or keys[pygame.K_s]:

                clock.tick(8 + Snake.length / 3)
                Player.move(Snake)

#CHECKS IF SNAKE EATS THE COOKIE

                if Cookie.x == Snake.x and Cookie.y == Snake.y:

                    Snack.newSnackDraw(Cookie, win)

                    Player.addBody(Snake)

                else:

                    Snake.turns.pop(0)

# CHECKS IF SNAKES HITS HIMSELF

            p = 0

            while p < len(Snake.turns) - 1:

                if Snake.x == Snake.turns[p][0] and Snake.y == Snake.turns[p][1]:

                    endTitles()

                p += 1

            for event in pygame.event.get():

                if event.type == pygame.QUIT:
                    pygame.quit()
                    exit()

            redrawGameWindow()

    runGame()

    pygame.quit()

main()
