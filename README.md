# MAZE-GAME-USING-HYBRID-DATA-STRUCTURES
import random
import tkinter as tk
import simple_colors

class Node:
    def __init__(self,pos,right=None,left=None,top=None,down=None):
        self.pos=pos #position of the node , each node is a position of a matrix
        self.right=right # node reference to right
        self.left=left
        self.top=top
        self.down=down
        #self.printNode()

    def printNode(self): #prints the attributes of node object
        print(self.pos,"right=",self.right,"left=",self.left
              ,"top=",self.top,"down=",self.down)

class maze:
    def __init__(self,matrix):
        self.matrix=matrix # 2D MATRIX WHERE 0 REPRESENTS EMPTY CELL AND 1 IS A WELL
        self.visited=[] #VISTED NODES FROM STARTING POSITION TO TARGET POSITION
        self.sequence=[] #CURRENT PATH
        self.check=1
        self.entry=0
        self.exit=0
        self.entry1=0
        self.variable1=len(self.matrix[0])
        self.variable2=len(self.matrix[len(matrix)-1])
        """Variable1 will be 10, variable2 will be 10, and variable3 will be 9.

Variable1 is the length of the first row of the matrix, which is 10 in this case.
 Variable2 is the length of the last row of the matrix, which is also 10.
 Variable3 is the number of rows in the matrix, which is 9 in this case"""
        self.variable3=len(self.matrix)
        self.printMaze()
        self.findcells()
        self.Main_root=self.zero_t[0] #ROOT OF THE TREE OF ALL NODES
        self.constructTree(self.Main_root)
        self.findroute()

        #ZERO_T A LISTS OF TUPLES OF ALL EMPTY CELLS
    def printMaze(self): #PRINT 
        print()
        print(simple_colors.red("THE GIVE MATRIX FOR MAZE PATH IS\n"))
        for row in matrix:
            for item in row:
                print(item,end=' ')
            print()
        print()

    def findcells(self): #RETURNS ALL 0 IN A MATRIX
        
        """
        for i in range(len(self.matrix)):
            if self.matrix[i][0]==1:
                self.entry1+=1
                """
        for i in range(len(self.matrix[0])):
            if self.matrix[0][i]==1:
                self.entry+=1
        for i in range(len(self.matrix[len(self.matrix)-1])):
            if self.matrix[len(self.matrix)-1][i]==1:
                self.exit+=1
        if(self.variable1==self.entry or self.variable2==self.exit ):
            self.check=0
        self.zero_t=[] # to find no of possible paths
        for i,e in enumerate(matrix): # later used to construct a tree 
            for j,ee in enumerate(e): #in built function for lists
                if matrix[i][j]==0:
                    self.zero_t.append((i,j))
        self.target=self.zero_t[len(self.zero_t)-1] # set target to last empty cell position
        return self.zero_t

    def constructTree(self,pos):  # this method is used to find all possible paths from a starting point
        #print() 
        T=Node(pos)
        paths=self.findpaths(matrix,self.zero_t,pos)
        #print(paths)
        #print(len(paths))
        if(len(paths)>0):
            for i in range(len(paths)):
                self.sequence.append(list(paths.values())[i])
                direction=list(paths.keys())[i]
                if direction == (0,1):
                    T.right=list(paths.values())[i]
                elif direction == (-1,0):
                    T.left=list(paths.values())[i]
                elif direction == (1,0):
                    T.top=list(paths.values())[i]
                elif direction == (0,-1):
                    T.down=list(paths.values())[i]
                #T.printNode() 
        for i in range(len(paths)):
            self.constructTree(list(paths.values())[i])
                    
    def findpaths(self,matrix,cells,root): # sees all possible moves 
        """ to find all possible paths from the current node"""
        #print(root)
        path={} # empty dictonary 
        if root not in self.visited:
            directions = [(0, 1), (1, 0), (-1, 0), (0, -1)]
            for direction in directions:
                new_x = root[0] + direction[0]
                new_y = root[1] + direction[1]
                """ 
                building the entire tree for all possible paths through the maze
                 if the current position is (3, 4) and the irection is (0, 1) (right), then the new position would be (3, 5)"""
                
                if 0 <= new_x <= (len(matrix)-1) and 0 <= new_y <= len(matrix) and matrix[new_x][new_y] == 0:
                    #print((new_x,new_y),direction)
                    path[direction]=((new_x,new_y))
        self.visited.append(root)
        return path
    """
    the method returns the path dictionary, which contains all the valid directions and 
    positions that can be taken from the given root position."""
    """
    The findpaths method returns a dictionary of all the
      valid directions (as keys) and their corresponding positions (as values)."""

    def findroute(self):
        #print(self.visited)
        sequence=[]
        for i in range(len(self.visited)):
            if(self.visited[i]!=self.target):
                if self.visited[i] not in sequence:
                    sequence.append(self.visited[i])
                else:           
                    sequence.pop()
            else:
                sequence.append(self.visited[i])
                break
        #print(sequence)
        #print(len(sequence)-1)
        if(sequence[len(sequence)-1])==self.target:
            print(simple_colors.green("PATH FOUND",['bold','underlined']))
            self.printRoute(sequence)
        else:
            print(simple_colors.yellow("PATH NOT FOUND",['bold','underlined']))
            self.check=0

        return sequence
    
    def printRoute(self,sequence):
        print()
        for i,e in enumerate(matrix):
            for j,ee in enumerate(e):
                if (i,j) in sequence:
                    print(simple_colors.magenta("x",['bold','underlined']),end=" ")
                else:
                    print(matrix[i][j],end=" ")
            print()
        print()


class MazeGUI:
    def __init__(self, matrix):
        self.matrix = matrix
        self.root = tk.Tk()
        self.canvas = tk.Canvas(self.root, width=400, height=400)
        self.canvas.pack()
        self.maze = maze(matrix)
        self.path = self.maze.findroute()
        self.temp()
        self.root.mainloop()

    def temp(self):
        if self.maze.check==1:
            self.found()
        else:
            self.notfound()

    def found(self):
        self.draw_maze()
        self.draw_path()
        self.canvas.create_text(200, 175, text="Path Found", fill="red", font=("Arial", 20, "bold"))

    def notfound(self):
        self.draw_maze()
        self.canvas.create_text(200, 175, text="Path Not Found", fill="red", font=("Arial", 20, "bold"))

    def draw_maze(self):
        for i in range(len(self.matrix)):
            for j in range(len(self.matrix[i])):
                x1, y1 = j * 40, i * 40
                x2, y2 = x1 + 40, y1 + 40
                if self.matrix[i][j] == 1:
                    self.canvas.create_rectangle(x1, y1, x2, y2, fill='grey')
                else:
                    if (i, j) in self.path  and self.maze.check==1:
                        self.canvas.create_rectangle(x1, y1, x2, y2, fill='white')
                        self.canvas.create_rectangle(x1, y1, x2, y2, fill='light green')
                    else:
                        self.canvas.create_rectangle(x1, y1, x2, y2, fill='white')

                        # Add this additional code to color the blocks with lines
                        if (i, j) in self.path and self.matrix[i][j] == 1:
                            self.canvas.create_rectangle(x1, y1, x2, y2, fill='yellow')


    def draw_path(self):
        for i in range(len(self.path) - 1):
            x1, y1 = self.path[i][1] * 40 + 20, self.path[i][0] * 40 + 20
            x2, y2 = self.path[i + 1][1] * 40 + 20, self.path[i + 1][0] * 40 + 20




# matrix = [[random.randint(0, 1) for _ in range(10)] for _ in range(10)]

matrix = [[1, 0, 0, 1, 0, 1, 0, 0, 0, 0],
          [0, 0, 1, 0, 1, 1, 1, 1, 0, 1],
          [0, 0, 0, 1, 1, 0, 0, 1, 0, 0],
          [0, 0, 1, 0, 1, 1, 1, 0, 0, 0],
          [1, 0, 1, 0, 0, 0, 0, 1, 0, 0],
          [1, 0, 0, 1, 1, 0, 1, 1, 0, 0],
          [0, 0, 0, 0, 0, 0, 0, 0, 0, 1],
          [1, 0, 1, 0, 1, 0, 1, 1, 0, 0],
          [0, 0, 1, 0, 0, 1, 0, 1, 0, 0]]
""""
matrix= [[0, 0, 1, 0, 0, 0, 0, 1, 0, 0],
          [1, 0, 1, 0, 1, 0, 1, 1, 0, 1],
          [0, 0, 0, 1, 1, 0, 1, 1, 0, 0],
          [1, 0, 1, 0, 1, 1, 1, 0, 0, 1],
          [0, 0, 1, 0, 0, 0, 0, 1, 0, 0],
          [0, 0, 0, 1, 1, 0, 1, 1, 0, 0],
          [0, 0, 0, 0, 0, 0, 0, 0, 1, 1],
          [1, 0, 1, 0, 1, 0, 0, 1, 0, 0],
          [0, 0, 1, 0, 0, 1, 0, 1, 1, 1]]


matrix= [[1, 0, 1, 0, 0, 0, 1, 0, 0, 0],
          [0, 0, 1, 0, 1, 1, 1, 1, 0, 1],
          [1, 0, 0, 1, 1, 0, 1, 1, 0, 0],
          [0, 0, 1, 0, 1, 1, 1, 0, 0, 0],
          [0, 0, 1, 0, 0, 0, 1, 1, 0, 0],
          [0, 0, 0, 1, 1, 0, 1, 0, 1, 0],
          [0, 0, 0, 1, 0, 0, 0, 0, 0, 1],
          [1, 0, 1, 0, 1, 0, 1, 1, 0, 0],
          [0, 0, 1, 1, 0, 0, 0, 0, 1, 1]]

matrix = [[1, 0, 1, 0, 0, 0, 1, 1, 0, 0],
          [1, 0, 1, 0, 1, 1, 0, 1, 0, 1],
          [0, 0, 0, 1, 1, 0, 1, 0, 0, 0],
          [0, 0, 1, 0, 1, 0, 1, 0, 0, 0],
          [0, 0, 1, 0, 0, 0, 1, 1, 0, 0],
          [1, 0, 0, 1, 1, 0, 1, 1, 0, 0],
          [0, 0, 1, 0, 0, 0, 0, 0, 0, 1],
          [0, 1, 1, 1, 1, 1, 1, 1, 0, 0],
          [1, 1, 0, 1, 0, 0, 0, 1, 1, 1]]

matrix= [[0, 0, 0, 1, 0, 0, 0, 0, 0, 1],
          [1, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          [1, 1, 1, 0, 0, 1, 0, 0, 1, 1],
          [0, 1, 0, 0, 0, 0, 0, 1, 1, 1],
          [0, 1, 0, 1, 1, 1, 0, 0, 0, 1],
          [0, 1, 1, 0, 0, 1, 0, 0, 1, 1],
          [0, 1, 1, 0, 1, 0, 1, 0, 1, 0],
          [0, 1, 0, 0, 0, 0, 0, 0, 1, 1],
          [0, 0, 0, 0, 0, 0, 0, 0, 0, 1]]
"""
""""
matrix= [
    [0, 1, 0, 0, 1, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 1, 1, 1, 1, 1, 1],
    [0, 0, 0, 0, 1, 0, 1, 1, 0, 0],
    [1, 0, 1, 1, 0, 1, 1, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 1, 1, 1, 0],
    [1, 0, 0, 1, 1, 0, 1, 1, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 1],
    [1, 0, 1, 0, 1, 0, 1, 1, 0, 0],
    [0, 0, 0, 1, 0, 1, 0, 1, 0, 0],
]

"""
MazeGUI(matrix)
