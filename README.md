# Java DNS Resolver

This project is a caching DNS resolver implemented in Java.

## Overview

The caching DNS resolver listens for incoming DNS requests and checks its local cache for valid responses. If a valid response is found in the cache, it sends the result back immediately; otherwise, it forwards the request to Google's public DNS server (8.8.8.8), stores the response in the local cache, and then sends back the response. The resolver supports basic lookup queries and handles DNS message specification effectively.

## Implementation Details

- **DNSHeader**: Represents the DNS header and provides methods to decode, encode, and manipulate header data.
- **DNSQuestion**: Represents a client request and provides methods to decode, encode, and manipulate question data.
- **DNSRecord**: Represents DNS records and provides methods to decode, encode, and manipulate record data.
- **DNSMessage**: Represents an entire DNS message and provides methods to decode, encode, and manipulate message data.
- **DNSCache**: Represents the local cache and provides methods for querying and inserting records into the cache.
- **DNSServer**: Represents the DNS server and handles incoming DNS requests by forwarding queries, processing responses, and managing the cache.

## Testing

To test the implementation, you can use the `dig` command-line tool to send DNS queries to the resolver. For example:
```
dig example.com @127.0.0.1 -p 8053
```
Wireshark can be used to ensure the correct format of messages exchanged between the resolver and clients.

## Error Handling

The resolver is robust to basic queries from `dig`, including queries for non-existent hosts.

## Memo about format

The header contains the following fields:

                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      ID                       |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |QR|   Opcode  |AA|TC|RD|RA|   Z    |   RCODE   |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    QDCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    ANCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    NSCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    ARCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
       

Resource record has the following format:

                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                                               |
    /                                               /
    /                      NAME                     /
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TYPE                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     CLASS                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TTL                      |
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                   RDLENGTH                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--|
    /                     RDATA                     /
    /                                               /
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    
Message compression: (domain name)

    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    | 1  1|                OFFSET                   |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
