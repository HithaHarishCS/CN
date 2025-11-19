#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>   // Needed for inet_addr()
#include <string.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    int sockfd, portno, n;
    char buffer[256];
    char c[20000];   // increased buffer size
    struct sockaddr_in serv;

    if (argc < 3)
    {
        printf("Usage: %s <server_ip> <port>\nExample: %s 127.0.0.1 7777\n", argv[0], argv[0]);
        exit(1);
    }

    // create socket
    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0)
    {
        perror("socket error");
        exit(1);
    }

    // fill server structure
    memset(&serv, 0, sizeof(serv));
    portno = atoi(argv[2]);

    serv.sin_family = AF_INET;
    serv.sin_port = htons(portno);
    serv.sin_addr.s_addr = inet_addr(argv[1]);   // *** REQUIRED ***

    // connect
    if (connect(sockfd, (struct sockaddr *)&serv, sizeof(serv)) < 0)
    {
        perror("connect failed");
        exit(1);
    }

    printf("Enter filename with complete path:\n");
    scanf("%255s", buffer);

    // send file request
    if (write(sockfd, buffer, strlen(buffer)) < 0)
    {
        perror("write error");
        exit(1);
    }

    memset(c, 0, sizeof(c));
    printf("Reading...\n");

    // read file content
    n = read(sockfd, c, sizeof(c) - 1);
    if (n < 0)
    {
        perror("read error");
        exit(1);
    }

    printf("\n--- File Content: %s ---\n\n", buffer);
    printf("%s\n", c);

    close(sockfd);
    return 0;
}
