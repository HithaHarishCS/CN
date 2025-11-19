#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <string.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    int sockfd, newsockfd, portno, n;
    char buffer[256];
    char cc[20000];
    struct sockaddr_in serv, cli;
    FILE *fd;

    if (argc < 2)
    {
        printf("error: no port number\nUsage: %s <port>\n", argv[0]);
        exit(1);
    }

    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0)
    {
        perror("socket");
        exit(1);
    }

    portno = atoi(argv[1]);
    memset(&serv, 0, sizeof(serv));

    serv.sin_family = AF_INET;
    serv.sin_addr.s_addr = INADDR_ANY;
    serv.sin_port = htons(portno);

    if (bind(sockfd, (struct sockaddr *)&serv, sizeof(serv)) < 0)
    {
        perror("bind");
        close(sockfd);
        exit(1);
    }

    listen(sockfd, 10);

    socklen_t len = sizeof(cli);
    printf("Server: waiting for connection...\n");

    newsockfd = accept(sockfd, (struct sockaddr *)&cli, &len);
    if (newsockfd < 0)
    {
        perror("accept");
        close(sockfd);
        exit(1);
    }

    // Receive filename
    memset(buffer, 0, sizeof(buffer));
    n = read(newsockfd, buffer, sizeof(buffer) - 1);

    if (n < 0)
    {
        perror("read");
        close(newsockfd);
        close(sockfd);
        exit(1);
    }

    printf("\nServer received filename: %s\n", buffer);

    // Try opening file
    fd = fopen(buffer, "r");
    if (fd != NULL)
    {
        printf("Server: %s found. Reading file...\n", buffer);

        memset(cc, 0, sizeof(cc));

        // Read file safely
        char line[1024];
        while (fgets(line, sizeof(line), fd) != NULL)
        {
            if (strlen(cc) + strlen(line) < sizeof(cc) - 1)
            {
                strcat(cc, line);
            }
            else
            {
                printf("Warning: file too large, truncating output.\n");
                break;
            }
        }
        fclose(fd);

        // Send file contents
        n = write(newsockfd, cc, strlen(cc));
        if (n < 0)
            perror("write");

        printf("Transfer complete.\n");
    }
    else
    {
        printf("Server: file not found\n");
        n = write(newsockfd, "file not found", 15);
        if (n < 0)
            perror("write");
    }

    close(newsockfd);
    close(sockfd);

    return 0;
}
