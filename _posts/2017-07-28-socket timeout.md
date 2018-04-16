---
title : connection to a socket with non-blocking mode and set timeout in C and linux
category : 
tags :[c,linux,socket]
---

## OBJECTIVE

When someone is using a blocking socket he may find that if he use a wrong IP address or port as the socket parameter, he need to wait 4~5 minutes for the control back from the connction function to the main. For avoiding this problem , I found a solution on stackoverflow(always the best place to learn something new).[ref](https://stackoverflow.com/questions/2597608/c-socket-connection-timeout)

## SIMPLICITY FOR THE ANSWER

- change the socket to non-blocking by using fcntl()
- connect to the socket.
- use select() wait till the wirtable file descriptor numbers in equal to the number we expect or the timeout expried.
- done.


## THE CODE
This is basically the answer from the [ref](https://stackoverflow.com/questions/2597608/c-socket-connection-timeout). There is a little modification for fitting to my project. I use this before the real "create socket and connect to it " function.


```c
uint8 LAN_CHECK_IP(LANDATA* ptrLANDATA){

    u_short port;                /* user specified port number */
    char *addr = ptrLANDATA->ptrUserData->pIpAddr;                  /* will be a pointer to the address */
    struct sockaddr_in address;  /* the libc network address data structure */
    short int sock = -1;         /* file descriptor for the network socket */
    fd_set fdset;
    struct timeval tv;
    int so_error,ret=COMMON_ERR;

    

    port = ptrLANDATA->ptrUserData->portNum;
    

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = inet_addr(addr); /* assign the address */
    address.sin_port = htons(port);            /* translate int2port num */

    sock = socket(AF_INET, SOCK_STREAM, 0);
    fcntl(sock, F_SETFL, O_NONBLOCK);

    connect(sock, (struct sockaddr *)&address, sizeof(address));

    FD_ZERO(&fdset);
    FD_SET(sock, &fdset);
    tv.tv_sec = LAN_CONNECTION_TIMEOUT;             /* 10 second timeout */
    tv.tv_usec = 0;

    if (select(sock + 1, NULL, &fdset, NULL, &tv) == 1)
    {
        socklen_t len = sizeof so_error;

        getsockopt(sock, SOL_SOCKET, SO_ERROR, &so_error, &len);
        
        // printf("so_error: %d\n",so_error );
        if (so_error == 0) {
           
            ret = COMMON_SUCCESS;
    }

       
    
    close(sock);
    return ret;



}
```

