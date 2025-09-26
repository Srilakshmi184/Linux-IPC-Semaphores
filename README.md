# Linux-IPC-Semaphores
## NAME-SRILAKSHMI.B.H
## REG.NO-212224100057

# AIM:
To Write a C program that implements a producer-consumer system with two processes using Semaphores.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Sempahores

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that implements a producer-consumer system with two processes using Semaphores.
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <sys/wait.h>
#include <time.h>

#define NUM_LOOPS 10

// Only define union semun if the system headers did not provide it
#ifndef __APPLE__
union semun
{
    int val;               
    struct semid_ds *buf;  
    unsigned short int *array; 
    struct seminfo *__buf;
};
#endif

void wait_semaphore(int sem_set_id)
{
    struct sembuf sem_op;
    sem_op.sem_num = 0;
    sem_op.sem_op = -1;
    sem_op.sem_flg = 0;
    semop(sem_set_id, &sem_op, 1);
}

void signal_semaphore(int sem_set_id)
{
    struct sembuf sem_op;
    sem_op.sem_num = 0;
    sem_op.sem_op = 1;
    sem_op.sem_flg = 0;
    semop(sem_set_id, &sem_op, 1);
}

int main()
{
    int sem_set_id;
    union semun sem_val;
    int child_pid;

    sem_set_id = semget(IPC_PRIVATE, 1, 0600);
    if (sem_set_id == -1)
    {
        perror("semget");
        exit(1);
    }

    printf("Semaphore set created, semaphore set id '%d'.\n", sem_set_id);

    sem_val.val = 0;
    if (semctl(sem_set_id, 0, SETVAL, sem_val) == -1)
    {
        perror("semctl");
        exit(1);
    }

    child_pid = fork();

    if (child_pid < 0)
    {
        perror("fork");
        exit(1);
    }

    if (child_pid == 0)
    {
        for (int i = 0; i < NUM_LOOPS; i++)
        {
            wait_semaphore(sem_set_id);
            printf("Consumer: %d\n", i);
            fflush(stdout);
        }
        exit(0);
    }
   else
   {
        for (int i = 0; i < NUM_LOOPS; i++)
        {
            printf("Producer: %d\n", i);
            fflush(stdout);
            signal_semaphore(sem_set_id);
            usleep(500000);
        }

        wait(NULL);

        semctl(sem_set_id, 0, IPC_RMID, sem_val);
        printf("Semaphore removed.\n");
    }

    return 0;
}


```


## OUTPUT
$ ./sem.o 

<img width="1208" height="343" alt="Screenshot 2025-09-26 at 1 37 35 PM" src="https://github.com/user-attachments/assets/926ded23-b002-40d3-83f1-b63e1d8cf126" />


$ ipcs

<img width="1438" height="158" alt="Screenshot 2025-09-26 at 1 37 59 PM" src="https://github.com/user-attachments/assets/ec92aab8-86a9-438e-8918-4850f21b3564" />




# RESULT:
The program is executed successfully.
