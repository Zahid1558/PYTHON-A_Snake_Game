# PYTHON-A_Snake_Game
//a simple snake game called "python" made with c++ without using classes and oop


#include <iostream>
#include <conio.h>
#include <windows.h>
#include<bits/stdc++.h>
using namespace std;
int difficulty=25;
bool gameOver;
const int width = 40;
const int height = 20;
int x, y, fruitX, fruitY, score;
int tailX[100], tailY[100];
int nTail;
enum eDirecton { STOP = 0, LEFT, RIGHT, UP, DOWN };
eDirecton dir;
void settings();
void exit_print();

struct players{
    char name[1000];
    int  score;
    struct players *next;
}*head=NULL;

void addelement(int n)
{
    HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

    score=0;
    struct players* neww, *current=head,*previous=NULL;
    neww=(struct players*)malloc(sizeof(struct players));
    cout<<"\n == You've scored "<<n<<"\n == Enter your name : ";
    fflush(stdin);
    gets(neww->name);
    neww->score= n;
    neww->next=NULL;
    if(head==NULL){
        head=neww;
    }
    else{
        int p=head->score;
        if(n>=p){
            system("cls");
            SetConsoleTextAttribute(hConsole, 12);
            cout<<"\n\n\n\t\t****CONGRATS   "<<neww->name<<"****\n\n\n\t You've made a new HIGHSCORE !!!\n\n";
            SetConsoleTextAttribute(hConsole, 10);

            neww->next=head;
            head=neww;
            cout<<"\n\n\t\tPress any key to Continue.\n";
            getch();
            return;
        }
        else if((head->next)==NULL)
        {
            head->next=neww;
            return;
        }
        else
        {
            previous=current;
            current=current->next;
            while(current!=NULL)
            {
                int ps=previous->score;
                int cs=current->score;
                if(n<ps&&n>=cs){
                   previous->next=neww;
                   neww->next=current;
                   return;
                }
                previous=current;
                current=current->next;
            }
        }
    }

    return;
}
void printelement(){
    HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(hConsole, 10);
    int i,j;
    struct players *current=head;
    cout<<"\n\n\t\t\t== LEADERBOARDS ==\n\n";
    SetConsoleTextAttribute(hConsole, 11);
    if(head==NULL){
        cout<<"\n\n\t\tNo RECORDS to show.\n";
    }
    while(current!=NULL){
        cout<<"\n\t\t=== "<<current->name<<"  ====  "<<current->score;
        current=current->next;
    }
   SetConsoleTextAttribute(hConsole, 10);
   printf("\n\n\t\tPress any key to Return\n");
   SetConsoleTextAttribute(hConsole, 11);
    char ch=getch();
    if(ch)return;
}
void load()
{

    FILE *fp;
    fp = fopen("file.txt", "rb");

    if(fp == NULL)
    {
        printf("Error reading file\n");
        return;
    }
    struct players *current_read, *current_node;
    while(1)
    {
        current_read = (struct players*)malloc(sizeof(struct players));
        if(fread(current_read, sizeof(struct players), 1, fp) != 1)
        {
            if(feof(fp))
            {
                return;
            }
            printf("\n\n\t\tNo record to show.\n\n");
            return;
        }

        current_read -> next = 0;

        if(head == NULL)
        {
            head = current_read;
        }
        else
        {
            current_node = head;
            while(current_node -> next != NULL)
            {
                current_node = current_node -> next;
            }
            current_node -> next = current_read;
        }
    }
    return;
}
void print_in_file()
{
    FILE* fp=fopen("file.txt","wb");
    if(!fp){
        printf("Error !");
        exit(1);
    }
    struct players *current=head;
    while(current!=NULL){
        if(fwrite(current,sizeof(struct players),1,fp)!=1)
            {
                exit(1);
            }
        current = current->next;
    }
    fclose(fp);
    exit(1);
}
void Setup()
{
	gameOver = false;
	dir = STOP;
	x = width / 2;
	y = height / 2;
	//x = 39;
	//y = 19;
	fruitX = rand() % width;
	fruitY = rand() % height;
	score = 0;
	nTail=0;
}
void Draw()
{
	HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);


	system("cls"); //system("clear");
	SetConsoleTextAttribute(hConsole, 10);
	cout << "----=======-----==========-----=======----";
	SetConsoleTextAttribute(hConsole, 47);
	cout << "\n>>>>>------------PYTHON--------------<<<<<\n";
	SetConsoleTextAttribute(hConsole, 10);
	cout << "----=======-----==========-----=======----\n\n";

	SetConsoleTextAttribute(hConsole, 11);
	cout << "  == Score:" << score << "\n";
	cout << " \n  == press P to PAUSE == press R to RESTART == press Q to QUIT ==\n\n";
	SetConsoleTextAttribute(hConsole, 15);
	for (int i = 0; i < width + 2; i++)
		cout << "#";
	cout << "\n";

	for (int i = 0; i < height; i++)
	{
		for (int j = 0; j < width; j++)
		{
			if (j == 0)
				cout << "#";
			if (i == y && j == x)
				{SetConsoleTextAttribute(hConsole, 10);cout << "@";SetConsoleTextAttribute(hConsole, 15);}
			else if (i == fruitY && j == fruitX)
			{
				SetConsoleTextAttribute(hConsole, 14); cout << "F"; SetConsoleTextAttribute(hConsole, 15);
			}
			else
			{
				bool print = false;
				for (int k = 0; k < nTail; k++)
				{
					if (tailX[k] == j && tailY[k] == i)
					{
						SetConsoleTextAttribute(hConsole, 10);cout << "O";SetConsoleTextAttribute(hConsole, 15);
						print = true;
					}
				}
				if (!print)
					cout << " ";
			}


			if (j == width - 1)
				cout << "#";
		}
		cout << "\n";
	}

	for (int i = 0; i < width + 2; i++)
		cout << "#";
	cout << "\n";
	//cout <<playername;

}
void game();
void menu();
void Input()
{
	HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	if (_kbhit())
	{
		switch (_getch())
		{
		case 'a':
			if(dir!=RIGHT)dir = LEFT;
			break;
		case 'd':
			if(dir!=LEFT)dir = RIGHT;
			break;
		case 'w':
			if(dir!=DOWN)dir = UP;
			break;
		case 's':
			if(dir!=UP)dir = DOWN;
			break;
		case '0':
			gameOver = true;
			break;
		case 'p':
			SetConsoleTextAttribute(hConsole,13);
			cout<<"\n=====You've PAUSED the Game=====\n\n";SetConsoleTextAttribute(hConsole, 15);
			system("pause");
			break;
		case 'r':
            game();
            break;
        case 'q':
            menu();
            break;
		}
	}
}
void Logic()
{
	int prevX = tailX[0];
	int prevY = tailY[0];
	int prev2X, prev2Y;
	tailX[0] = x;
	tailY[0] = y;
	for (int i = 1; i < nTail; i++)
	{
		prev2X = tailX[i];
		prev2Y = tailY[i];
		tailX[i] = prevX;
		tailY[i] = prevY;
		prevX = prev2X;
		prevY = prev2Y;
	}
	switch (dir)
	{
	case LEFT:
		x--;
		break;
	case RIGHT:
		x++;
		break;
	case UP:
		y--;
		break;
	case DOWN:
		y++;
		break;
	default:
		break;
	}
	//if (x > width || x < 0 || y > height || y < 0)
	  //gameOver = true;
	if (x >= width) x = 0; else if (x < 0) x = width - 1;
	if (y >= height) y = 0; else if (y < 0) y = height - 1;

	for (int i = 0; i < nTail; i++)
		if (tailX[i] == x && tailY[i] == y)
			gameOver = true;

	if (x == fruitX && y == fruitY)
	{
		cout<<"\a";
		score += 10;
		fruitX = rand() % width;
		fruitY = rand() % height;
		nTail++;
	}
}
void game()
{
	HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(hConsole, 10);

	Setup();
	while (!gameOver)
	{
		Draw();
		Input();
		Logic();
		Sleep(difficulty);
	}
	SetConsoleTextAttribute(hConsole, 12);cout<<"\n\n\t GAME OVER !!!   :(\n\n";SetConsoleTextAttribute(hConsole, 11);
             addelement(score);
	return;
}
void welcome()
{
    HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(hConsole, 10);
printf( "\n                 _                   .-=-.          .-==-.");
printf( "\n                { }      __        .' O o '.       /  -<' )");
printf( "\n                 { }    .' O'.     / o .-. O \     /  .--v`");
printf( "\n                  { }   / .-. o\   /O  /   \  o\   /O /");
printf( "\n                   { `-` /   \ O`-'o  /     \  O`-`o /");
printf( "\n                    `-.-`     '.____.'       `.____.'\n\n");
printf("                               _   _                 \n");
printf("                   _ __  _   _| |_| |__   ___  _ __  \n");
printf("                  | '_ \\| | | | __| '_ \\ / _ \\| '_ \\ \n");
printf("                  | |_) | |_| | |_| | | | (_) | | | |\n");
printf("                  | .__/ \\__, |\\__|_| |_|\\___/|_| |_|\n");
printf("                  |_|    |___/\n");
printf("                                      ======a snake game programmed in C\n\n");
return;
}

void instructions()
{   HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

    system("cls");
    cout<<"\n\n\t\t\t=== INSTRUCTIONS ===\n";
    SetConsoleTextAttribute(hConsole, 11);
    cout<<"\n\n\t\tUse W, A, S, D keys for movement.\n\n\t\tEat the fruits to earn scores('$').\n\n\t\tDont bite your own Tail !\n\n\t\tENJOY !!!!\n\n";
	SetConsoleTextAttribute(hConsole, 10);
	cout<<"\n\n\t\tPress any key to Return\n";
    char ch;
    ch=getch();
    if(ch)return;

}


void menu()
{
    HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(hConsole, 10);


    int i;
    char ch;
    while(1){
        i=1;
        while(1){

            if(i==1){
                system("cls");
                welcome();
                SetConsoleTextAttribute(hConsole, 11);
                printf("\t\t==>>\t1. PLAY\n\n\t\t2. settings\n\n\t\t3. instructions\n\n\t\t4. leaderboards\n\n\t\t5. exit\n\n");
            }
            else if(i==2){
                system("cls");
                welcome();
                SetConsoleTextAttribute(hConsole, 11);
                printf("\t\t1. play\n\n\t\t==>\t2. SETTINGS\n\n\t\t3. instructions\n\n\t\t4. leaderboards\n\n\t\t5. exit\n\n");
            }
            else if(i==3){
                system("cls");
                welcome();
                SetConsoleTextAttribute(hConsole, 11);
                printf("\t\t1. play\n\n\t\t2. settings\n\n\t\t==>\t3. INSTRUCTIONS\n\n\t\t4. leaderboards\n\n\t\t5. exit\n\n");
            }
            else if(i==4){
                system("cls");
                welcome();
                SetConsoleTextAttribute(hConsole, 11);
                printf("\t\t1. play\n\n\t\t2. settings\n\n\t\t3. instructions\n\n\t\t==>\t4. LEADERBOARDS\n\n\t\t5. exit\n\n");
            }
            else if(i==5){
                system("cls");
                welcome();
                SetConsoleTextAttribute(hConsole, 11);
                printf("\t\t1. play\n\n\t\t2. settings\n\n\t\t3. instructions\n\n\t\t4. leaderboards\n\n\t\t==>\t5. EXIT\n\n");
            }

            SetConsoleTextAttribute(hConsole, 10);printf("\n\tPress UP ans DOWN key to navigate the menu\n");

            ch=getche();
            if(ch==72){
                system("cls");
                i--;
                if(i==0)i=5;
                continue;
            }
            else if(ch==80){
                system("cls");
                i++;
                if(i==6)i=1;
                continue;
            }
            else if(ch==13){
                system("cls");
                break;
            }

        }
        if(i==1){
             game();
        }
        else if(i==2){
           settings();
        }
        else if(i==3)
        {
            instructions();
        }
        else if(i==4)
        {   printelement();
        }
        else if(i==5)
        {   exit_print();
            print_in_file();
            return;

        }

    }
    return;
}


void exit_print()
{
    HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hConsole, 10);
    cout<<"\n\t\tDeveloped by\n";
    cout<<"\t\t============\n";
     SetConsoleTextAttribute(hConsole, 11);
    cout<<"\n\tMd. Zahidul Islam\n";
    cout<<"\tShafkat Rahman Farabi\n";
    cout<<"\tMohammad Sabik Irbaaz\n";
    cout<<"\n\t\t\t CSE , IUT\n\n\n";
     SetConsoleTextAttribute(hConsole, 10);
     SetConsoleTextAttribute(hConsole, 10);
     cout<<"\tPress any key to Quit.\n\n";
     getch();
     return ;
}
void settings()
{
    HANDLE  hConsole;
	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleTextAttribute(hConsole, 10);
    int i;
    char ch;
    while(1){
        i=1;
        while(1){

            if(i==1){

                system("cls");printf("\n\n\t==SET your DIFFICULTY ==\n\n");printf("\t\t==>>\t1. BEGINNER\n\n\t\t2. intermediate\n\n\t\t3. pro\n\n");
            }
            else if(i==2){

                system("cls");printf("\n\n\t==SET your DIFFICULTY ==\n\n");printf("\t\t1. beginner\n\n\t\t==>\t2. INETERMEDIATE\n\n\t\t3. pro\n\n");
            }
            else if(i==3){

                system("cls");printf("\n\n\t==SET your DIFFICULTY ==\n\n");printf("\t\t1. beginner\n\n\t\t2. intermediate\n\n\t\t==>\t3. PRO\n\n");
            }
            SetConsoleTextAttribute(hConsole, 11);printf("\n\tPress UP ans DOWN key to navigate\n");SetConsoleTextAttribute(hConsole, 10);

            ch=getche();
            if(ch==72){
                system("cls");
                i--;
                if(i==0)i=3;
                continue;
            }
            else if(ch==80){
                system("cls");
                i++;
                if(i==4)i=1;
                continue;
            }
            else if(ch==13){
                system("cls");
                break;
            }

        }
        if(i==1){
            difficulty=75;
            return;
        }
        else if(i==2){
            difficulty=50;
            return;
        }
        else if(i==3) {
            difficulty=25;
            return;
        }

    }
}
int main()
{
      load();
      menu();
      return 0;

}
