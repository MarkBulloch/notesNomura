.hk.buildFromS3.run:{[dt;tab;function]
    $[system"e";
        .hk.buildFromS3.runInner[dt;tab;function];
        .Q.trp[.hk.buildFromS3.runInner[dt;tab]; function; .hk.buildFromS3.handleError]];
 };
 
 $[system"e";] - if else statement, system"e" enables error trapping for client requests:
0	When a client request has an error, by default the server clears the stack. Appropriate for production use as it enables the server to continue processing other client requests.
1	The server suspends on an error, and does not process other requests until the stack is cleared. Appropriate for development: enables debugging on the server.

.Q.trp - is an extended form of trap which collects the back trace - will trap the error and run the .handleError logic if requiredS
 
