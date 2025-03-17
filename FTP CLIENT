#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int main() {
int server_sock;
struct sockaddr_in server_addr;
server_sock = socket(AF_INET, SOCK_STREAM, 0);
if(server_sock < 0) {
perror("Socket error");
exit(1);
}
memset(&server_addr, '\0', sizeof(server_addr));
server_addr.sin_family = AF_INET;
server_addr.sin_port = htons(8080);
server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
connect(server_sock, (struct sockaddr*)&server_addr, sizeof(server_addr));

char buffer[1024], filename[100];
FILE *fp;
int choice;
printf("1. Upload a file (PUT)\n2. Download a file (GET)\n3. Exit\nEnter choice: ");
scanf("%d", &choice);

switch(choice) {
case 1:
bzero(buffer, 1024);
strcpy(buffer, "PUT");
send(server_sock, buffer, 1024, 0);
printf("Enter filename: ");
scanf("%s", filename);
fp = fopen(filename, "r");
if(fp == NULL) {
printf("File doesn't exist\n");
} else {
send(server_sock, filename, 1024, 0);
bzero(buffer, 1024);
recv(server_sock, buffer, 1024, 0);
if(strcmp(buffer, "READY") == 0) {
while(!feof(fp)) {
bzero(buffer, 1024);
fread(buffer, 1, 1024, fp);
send(server_sock, buffer, 1024, 0);
}
fclose(fp);
printf("File %s sent\n", filename);
}
}
break;

case 2:
bzero(buffer, 1024);
strcpy(buffer, "GET");
send(server_sock, buffer, 1024, 0);
printf("Enter filename: ");
scanf("%s", filename);
send(server_sock, filename, 1024, 0);
bzero(buffer, 1024);
recv(server_sock, buffer, 1024, 0);
if(strcmp(buffer, "READY") == 0) {
fp = fopen(filename, "w");
while(1) {
bzero(buffer, 1024);
recv(server_sock, buffer, 1024, 0);
if(strcmp(buffer, "END") == 0)
break;
fwrite(buffer, 1, 1024, fp);
}
fclose(fp);
printf("File %s received\n", filename);
}
break;

case 3:
bzero(buffer, 1024);
strcpy(buffer, "EXIT");
send(server_sock, buffer, 1024, 0);
close(server_sock);
exit(0);
}
return 0;
}
