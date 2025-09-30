IPC-MECHANISM
PIPE:
Pipes allow one-way communication between parent and child processes.

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
    return 0;
}
