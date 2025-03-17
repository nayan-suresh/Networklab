#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

void main() {
int server_sock, client_sock;
struct sockaddr_in server_addr, client_addr;
socklen_t addr_size;
server_sock = socket(AF_INET, SOCK_STREAM, 0);
if(server_sock < 0) {
perror("Socket error");
exit(1);
}
memset(&server_addr, '\0', sizeof(server_addr));
server_addr.sin_family = AF_INET;
server_addr.sin_port = htons(8080);
server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
if(bind(server_sock, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0) {
perror("Bind error");
exit(1);
}
listen(server_sock, 1024);
addr_size = sizeof(client_addr);
client_sock = accept(server_sock, (struct sockaddr*)&client_addr, &addr_size);
char buffer[1024], filename[100];
while(1) {
bzero(buffer, 1024);
recv(client_sock, buffer, 1024, 0);
if(strcmp(buffer, "LIST") == 0) {
bzero(buffer, 1024);
system("ls > temp.txt");
FILE *fp = fopen("temp.txt", "r");
while(!feof(fp)) {
bzero(buffer, 1024);
fgets(buffer, 1024, fp);
send(client_sock, buffer, 1024, 0);
}
fclose(fp);
remove("temp.txt");
}
else {
strcpy(filename, buffer);
FILE *fptr = fopen(filename, "r");
if(fptr == NULL) {
printf("File %s does not exist\n", filename);
strcpy(buffer, "File not found");
send(client_sock, buffer, 1024, 0);
}
else {
strcpy(buffer, "READY");
send(client_sock, buffer, 1024, 0);
while(!feof(fptr)) {
bzero(buffer, 1024);
fread(buffer, 1, 1024, fptr);
send(client_sock, buffer, 1024, 0);
}
fclose(fptr);
printf("File %s sent\n", filename);
}
}
if(strcmp(buffer, "EXIT") == 0) {
close(server_sock);
exit(0);
}
}
}
