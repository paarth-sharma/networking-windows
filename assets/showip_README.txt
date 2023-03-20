Port showip.c to Windows:

1. Replace socket headers with winsock2.h

2. Include ws2tcpip.h

3. Unlike most Windows APIs, you do not need to include windows.h before
   including winsock2.h.  If windows.h is needed, add these lines before
   including any windows headers:
   #ifndef WIN32_LEAN_AND_MEAN
   #define WIN32_LEAN_AND_MEAN
   #endif

   This will prevent windows.h from including winsock.h which will cause
   duplicate definition errors when winsock2.h is included.

   Note that this prevents a number of headers from being included automatically
   so it may be necessary to include them as needed.

 4. It is necessary to initialize Winsock before using it.  This is also where
    you specify the desired version of Winsock.  Need Winsock version 2.2 to get
    IPv6 support.

   WSADATA wsaData; 
   int status = WSAStartup(MAKEWORD(2,2),wsaData);

   Check that status == 0.  If not, initialization failed.

   Check that both the low and high order bytes of wsaData.wVersion are 2.
   If not, version 2.2 is not available.

5. Call WSACleanup before all return and exit.

6. Add Ws2_32.lib to the list of needed libraries before linking.

7. Error handling is different than Unix/Linux.  In most cases, the error
   code is retrieved by calling WSAGetLastError.  FormatMessage is used to
   convert the error code to an error message.  errno is not used by Winsock
   so perror will not return useful information.

   Note that gai_strerror is not thread safe and its use is not recommended.

   WSAStartup  - 0 = Success.  On error, the function returns the erorr code.
                 Do NOT call WSAGetLastError.
   getaddrinfo - 0 = Success.  This function returns an error code but it may
                 not be the correct error code.  Call WSAGetLastError.

