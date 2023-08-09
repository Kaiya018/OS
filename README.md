# OS
OScode

#OSのスケジューリングアルゴリズムについて

#到着時間順と処理時間順のの動作方法
一番最初にプロセス数を入力し、その後ランダムにプロセスを生成するか自分でプロセスを設定するかを0or1で決める。その後実行結果が表示されるようになっている

#以下はプロセス数を４、ランダムにプロセスを生成した場合の到着時間順の処理結果
process_number:4
ramdom_process=0: imput_process=1 0
process1:arrive=2:processingtime=5
process2:arrive=1:processingtime=8
process3:arrive=9:processingtime=9
process4:arrive=2:processingtime=1
time 1
Task2 time 2
Task2 time 3
Task2 time 4
Task2 time 5
Task2 time 6
Task2 time 7
Task2 time 8
Task2 time 9
Task1 time 10
Task1 time 11
Task1 time 12
Task1 time 13
Task1 time 14
Task4 time 15
Task3 time 16
Task3 time 17
Task3 time 18
Task3 time 19
Task3 time 20
Task3 time 21
Task3 time 22
Task3 time 23
Task3 time 24
process1: arrive:2 finish:14 around:12
process2: arrive:1 finish:9 around:8
process3: arrive:9 finish:24 around:15
process4: arrive:2 finish:15 around:13
average_aroud=12.000000

#到着時間順code
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

typedef struct Process
{
    int time_arrive;
    int time_process;
    int time_finish;
    int time_around;
    int flag;
    int process_number;
} Process;

typedef struct Node
{
    Process infor;
    struct Node *next;
} Node;

Node *gen_node(Process p)
{
    Node *n = (Node *)malloc(sizeof(Node));
    n->infor.time_arrive = p.time_arrive;
    n->infor.time_process = p.time_process;
    n->next = NULL;
    n->infor.process_number = p.process_number;
    return n;
}

void add_process(Process p[], int N)
{
    int i;

    for (i = 0; i < N; ++i)
    {
        printf("arrive_process:");
        scanf("%d", &p[i].time_arrive);
        printf("processingtime:");
        scanf("%d", &p[i].time_process);
        p[i].flag = 0;
        p[i].process_number = i + 1;
    }
}

void create_process(Process p[], int N)
{
    srand((unsigned)time(NULL));

    for (int i = 0; i < N; ++i)
    {
        p[i].time_arrive = rand() % 10;
        p[i].time_process = rand() % 9 + 1;
        p[i].process_number = i + 1;
    }

    for (int i = 0; i < N; ++i)
    {
        printf("process%d:arrive=%d:processingtime=%d\n", p[i].process_number, p[i].time_arrive, p[i].time_process);
    }
}

void list_insert(Node **head, Process p)
{
    // new node
    Node *n = gen_node(p);

    if (*head == NULL)
    {
        *head = n;
        return;
    }

    Node *r;
    r = *head;

    if (r->next == NULL)
    {
        r->next = n;
        return;
    }

    while( r->next != NULL)
    {
        r = r->next;
    }

    r->next = n;
    
}

void check_process(int time, Process p[], int N, Node **head)
{
    int i;

    Process q;
    q.flag = 0;
    q.time_process = 0;

    for (i = 0; i < N; ++i)
    {
        if (p[i].time_arrive == time && p[i].flag == 0)
        {
            p[i].flag = 1;

            list_insert(head, p[i]);
        }
    }
}

void calcu(Process p[], int N)
{
    int time, j, all_flag;
    Process list;
    all_flag = N;
    time = 0;
    Node *head = NULL;

    while (all_flag != 0)
    {

        check_process(time, p, N, &head);

        if (head == NULL)
        {
            time++;
            printf("time %d\n", time);
            continue;
        }

        while ((*head).infor.time_process != 0)
        {

            time++;

            printf("Task%d ", (*head).infor.process_number);
            (*head).infor.time_process = (*head).infor.time_process - 1;

            check_process(time, p, N, &head);

            printf("time %d\n", time);

            if ((*head).infor.time_process == 0)
            {
                all_flag = all_flag - 1;
                for (j = 0; j < N; ++j)
                {
                    if (p[j].time_arrive == (*head).infor.time_arrive && p[j].process_number == (*head).infor.process_number)
                    {
                        p[j].time_finish = time;
                    }
                }
            }
        }

        if ((*head).next != NULL)
        {
            head = (*head).next;
            continue;
        }

        if (all_flag != 0)
        {
            time++;
            printf("time %d\n", time);
        }
    }
}

int main(void)
{
    int N, i, q;
    double ave_around = 0;
    Process p[10000];

    Node *head = NULL;

    printf("process_number:");
    scanf("%d", &N);

    printf("ramdom_process=0: imput_process=1 ");
    scanf("%d", &q);

    if (q == 0)
    {
        create_process(p, N);
    }
    else if (q == 1)
    {
        add_process(p, N);
    }
    else
    {
        return 0;
    }

    calcu(p, N);

    for (i = 0; i < N; ++i)
    {
        p[i].time_around = p[i].time_finish - p[i].time_arrive;
    }

    for (i = 0; i < N; ++i)
    {
        printf("process%d: arrive:%d finish:%d around:%d\n", p[i].process_number, p[i].time_arrive, p[i].time_finish, p[i].time_around);
        ave_around = ave_around + p[i].time_around;
    }

    printf("average_aroud=%f\n", ave_around / (double)N);

    return 0;
}

#以下はプロセス数を４、ランダムにプロセスを生成した場合の処理時間順の処理結果
process_number:4
ramdom_process=0: imput_process=1 0
process1:arrive=2:processingtime=9
process2:arrive=3:processingtime=1
process3:arrive=1:processingtime=8
process4:arrive=7:processingtime=1
time 1
Task3 time 2
Task3 time 3
Task3 time 4
Task3 time 5
Task3 time 6
Task3 time 7
Task3 time 8
Task3 time 9
Task2 time 10
Task4 time 11
Task1 time 12
Task1 time 13
Task1 time 14
Task1 time 15
Task1 time 16
Task1 time 17
Task1 time 18
Task1 time 19
Task1 time 20
process1: arrive:2 finish:20 around:18
process2: arrive:3 finish:10 around:7
process3: arrive:1 finish:9 around:8
process4: arrive:7 finish:11 around:4
average_aroud=9.250000

#処理時間順のcode
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

typedef struct Process
{
    int time_arrive;
    int time_process;
    int time_finish;
    int time_around;
    int flag;
    int process_number;
} Process;

typedef struct Node
{
    Process infor;
    struct Node *next;
} Node;

Node *gen_node(Process p)
{
    Node *n = (Node *)malloc(sizeof(Node));
    n->infor.time_arrive = p.time_arrive;
    n->infor.time_process = p.time_process;
    n->next = NULL;
    n->infor.process_number = p.process_number;
    return n;
}

void add_process(Process p[], int N)
{
    int i;

    for (i = 0; i < N; ++i)
    {
        printf("arrive_process:");
        scanf("%d", &p[i].time_arrive);
        printf("processingtime:");
        scanf("%d", &p[i].time_process);
        p[i].flag = 0;
        p[i].process_number = i + 1;
    }
}

void create_process(Process p[], int N)
{
    srand((unsigned)time(NULL));

    for (int i = 0; i < N; ++i)
    {
        p[i].time_arrive = rand() % 10;
        p[i].time_process = rand() % 9 + 1;
        p[i].process_number = i + 1;
    }

    for (int i = 0; i < N; ++i)
    {
        printf("process%d:arrive=%d:processingtime=%d\n", p[i].process_number, p[i].time_arrive, p[i].time_process);
    }
}

void list_insert(Node **head, Process p)
{
    // new node
    Node *n = gen_node(p);

    if (*head == NULL)
    {
        *head = n;
        return;
    }

    Node *r;
    r = *head;

    if (r->next == NULL)
    {
        r->next = n;
        return;
    }

    if (r->next->infor.time_process > n->infor.time_process)
    {

        n->next = (*head)->next;
        (*head)->next = n;
        return;
    }
    else if (r->next->infor.time_process <= n->infor.time_process)
    {

        while (r->next->infor.time_process <= n->infor.time_process)
        {

            r = r->next;

            if (r->next == NULL)
            {
                r->next = n;
                return;
            }

            if (r->next->infor.time_process > n->infor.time_process)
            {
                n->next = r->next;
                r->next = n;
                return;
            }
        }
    }
}

void check_process(int time, Process p[], int N, Node **head)
{
    int i;

    Process q;
    q.flag = 0;
    q.time_process = 0;

    for (i = 0; i < N; ++i)
    {
        if (p[i].time_arrive == time && p[i].flag == 0)
        {
            p[i].flag = 1;

            list_insert(head, p[i]);
        }
    }
}

void calcu(Process p[], int N)
{
    int time, j, all_flag;
    Process list;
    all_flag = N;
    time = 0;
    Node *head = NULL;

    while (all_flag != 0)
    {

        check_process(time, p, N, &head);

        if (head == NULL)
        {
            time++;
            printf("time %d\n", time);
            continue;
        }

        while ((*head).infor.time_process != 0)
        {

            time++;

            printf("Task%d ", (*head).infor.process_number);
            (*head).infor.time_process = (*head).infor.time_process - 1;

            check_process(time, p, N, &head);

            printf("time %d\n", time);

            if ((*head).infor.time_process == 0)
            {
                all_flag = all_flag - 1;
                for (j = 0; j < N; ++j)
                {
                    if (p[j].time_arrive == (*head).infor.time_arrive && p[j].process_number == (*head).infor.process_number)
                    {
                        p[j].time_finish = time;
                    }
                }
            }
        }

        if ((*head).next != NULL)
        {
            head = (*head).next;
            continue;
        }

        if (all_flag != 0)
        {
            time++;
            printf("time %d\n", time);
        }
    }
}

int main(void)
{
    int N, i, q;
    double ave_around = 0;
    Process p[10000];

    Node *head = NULL;

    printf("process_number:");
    scanf("%d", &N);

    printf("ramdom_process=0: imput_process=1 ");
    scanf("%d", &q);

    if (q == 0)
    {
        create_process(p, N);
    }
    else if (q == 1)
    {
        add_process(p, N);
    }
    else
    {
        return 0;
    }

    calcu(p, N);

    for (i = 0; i < N; ++i)
    {
        p[i].time_around = p[i].time_finish - p[i].time_arrive;
    }

    for (i = 0; i < N; ++i)
    {
        printf("process%d: arrive:%d finish:%d around:%d\n", p[i].process_number, p[i].time_arrive, p[i].time_finish, p[i].time_around);
        ave_around = ave_around + p[i].time_around;
    }

    printf("average_aroud=%f\n", ave_around / (double)N);

    return 0;
}


