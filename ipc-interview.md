IPC-MECHANISM
PIPE:
1Pipes allow one-way communication between parent and child processes.

Use pipe(), fork(), read(), and write() for communication.
Pipes allow one-way communication between parent and child processes.

Use pipe(), fork(), read(), and write() for communication.
EXAMPLE:
#include <stdio.h>
#include <unistd.h>
#include <string.h>

int main() {
    int fd[2];
    pid_t pid;
    char msg[] = "Hello from parent!";
    char buffer[100];

    pipe(fd);             // Create pipe
    pid = fork();         // Fork process

    if (pid > 0) {
        // Parent process - writer
        close(fd[0]);     // Close read end
        write(fd[1], msg, strlen(msg) + 1);
        close(fd[1]);
    } else {
        // Child process - reader
        close(fd[1]);     // Close write end
        read(fd[0], buffer, sizeof(buffer));
        printf("Child received: %s\n", buffer);
        close(fd[0]);
    }

    FIFO:
    FIFO is a named pipe used for communication between unrelated or related processes.

Created using mkfifo() or the shell command mkfifo.

Supports one-way communication unless two FIFOs are used.
Created using mkfifo() and accessed using standard file operations (open, read, write).

It follows First In First Out — the first data written is the first to be read.
fifowrite.c
#include <stdio.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <unistd.h>

int main() {
    int fd;
    char *fifo = "/tmp/myfifo";

    // Create the FIFO (named pipe)
    mkfifo(fifo, 0666);

    // Open FIFO for writing
    fd = open(fifo, O_WRONLY);
    write(fd, "Hello from writer!", 19);
    close(fd);

    return 0;
}
fiforead.c
#include <stdio.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <unistd.h>

int main() {
    int fd;
    char buffer[100];
    char *fifo = "/tmp/myfifo";

    // Open FIFO for reading
    fd = open(fifo, O_RDONLY);
    read(fd, buffer, sizeof(buffer));
    printf("Reader received: %s\n", buffer);
    close(fd);

    return 0;
}

MESSAGEQUEUE:Allows one or more processes to send and receive structured messages (not just bytes).

Messages are stored in a queue until they are retrieved.

e’ll create:

Sender process (writes a message)

Receiver process (reads the message)
Created and accessed using msgget(), msgsnd(), msgrcv(), and messages are retrieved based on message type.
// msg_struct.h
#define MAX_TEXT 100

struct msg_buffer {
    long msg_type;
    char msg_text[MAX_TEXT];
};
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include "msg_struct.h"

int main() {
    key_t key;
    int msgid;
    struct msg_buffer msg;

    key = ftok("progfile", 65);              // Generate unique key
    msgid = msgget(key, 0666 | IPC_CREAT);   // Create message queue

    msg.msg_type = 1;
    printf("Enter message: ");
    fgets(msg.msg_text, MAX_TEXT, stdin);

    msgsnd(msgid, &msg, sizeof(msg.msg_text), 0);  // Send message
    printf("Message sent.\n");

    return 0;
}
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include "msg_struct.h"

int main() {
    key_t key;
    int msgid;
    struct msg_buffer msg;

    key = ftok("progfile", 65);             // Generate same key
    msgid = msgget(key, 0666 | IPC_CREAT);  // Access message queue

    msgrcv(msgid, &msg, sizeof(msg.msg_text), 1, 0);  // Receive message

    printf("Received message: %s\n", msg.msg_text);

    // Delete message queueA 
    msgctl(msgid, IPC_RMID, NULL);

    return 0;
}

MAILBOX:

mailbox is an inter-task communication (IPC) mechanism used in real-time operating systems (RTOS) that allows tasks (or threads) to send and receive fixed-size messages,
Mailbox allows one task to send a message and another task to receive it.

Usually used in RTOS like FreeRTOS, Keil RTX, ThreadX,
It ensures safe communication and synchronization between producer and consumer tasks
Real-World Use Case

For example, a sensor task sends temperature data to a processing task via a mailbox — each reading is a message.
