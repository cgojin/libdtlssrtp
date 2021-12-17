# libdtlssrtp

## DTLS-SRTP library

Datagram Transport Layer Security (DTLS) Extension to Establish Keys for the Secure Real-time Transport Protocol (SRTP)	(DTLS-SRTP) Reference Implementation

This package provides an implementation of the Datagram Transport Layer Security Extension to Establish Keys for the Secure Real-time Transport Protocol (DTLS-SRTP).  These mechanisms are documented in the Internet Drafts rfc5764 and related articles.

The essence of this mechanism is to send and receive DTLS packets via the very same ports used by RTP (possible multiplexed with RTCP and STUN), i.e. a multiplex mechanism, which requires the UDP port used to perform DTLS handshaking must NOT be possessed by objects performing DTLS, as the port is also used to perform RTP communication. There also must be a mechanism to demultiplex DTLS packets from RTP ones when received.

Inspired by Asterisk(R)'s DTLS-SRTP implementation, this package uses OpenSSL to provide cryptography functionalities needed, and makes use of memory BIOs to buffer DTLS packets and to decouple SSL objects from sockets. Objects performing DTLS (dtls_sess, defined in dtls_srtp.h) never sned and receive packets via socket, instead using mechanisms already implemented for RTP to perform communication. Functionalities to send packets are abstracted as data sinks (defined in data_sink.h) able to be called by dtls_sess objects during handshaking. Received packets need to be checked by packet_is_dtls function (defined in dtls_srtp.h) to determine whether they are DTLS packets, and are further parsed accordingly.

Further details could be checked from comments within header files.

There is an example program (example.c) to demonstrate how to use this library to perform DTLS handshaking and get SRTP master key. There is also a simple exemplary data sink directly implemented atop UDP socket without timer functionality, used by the example program mentioned above.


## Build libdtlssrtp

```sh
git clone https://github.com/cgojin/libdtlssrtp
cd libdtlssrtp
make
```

## Build and run example

### Build example

```sh
git clone https://github.com/cgojin/libdtlssrtp
cd libdtlssrtp
make test
```
### Generate certificate

```sh
openssl genrsa -out server.key 2048
openssl ecparam -genkey -name secp384r1 -out server.key   # need define HAVE_OPENSSL_ECDH_AUTO
openssl req -new -x509 -sha256 -key server.key -out server.crt -days 365

openssl genrsa -out client.key 2048
openssl ecparam -genkey -name secp384r1 -out client.key   # need define HAVE_OPENSSL_ECDH_AUTO
openssl req -new -x509 -sha256 -key client.key -out client.crt -days 365
```

### Run server and client
```sh
# server
./dtlssrtp_example -s -k server.key -c server.crt -p 8899

# client bind on IPv4
./dtlssrtp_example -k client.key -c client.crt -b 127.0.0.1 127.0.0.1 8899

# client bind on IPv6
./dtlssrtp_example -k client.key -c client.crt -b ::1 ::1 8899
```

## Licenses

Copyright (C) Richfit Information Technology Co.Ltd.
Contributed by Xie Tianming <persmule@gmail.com>, 2015.

The DTLS-SRTP library is free software; you can redistribute it and/or
modify it under the terms of the GNU Lesser General Public
License as published by the Free Software Foundation; either
version 2.1 of the License, or (at your option) any later version.

The DTLS-SRTP library is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
Lesser General Public License for more details.

You should have received a copy of the GNU Lesser General Public
License along with the DTLS-SRTP library; if not, see
<http://www.gnu.org/licenses/>.
