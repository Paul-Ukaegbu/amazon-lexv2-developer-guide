# Event stream encoding<a name="event-stream-encoding"></a>

Event stream encoding provides bidirectional communication using messages between a client and a server\. Data frames sent to the Amazon Lex V2 streaming service are encoded in this format\. The response from Amazon Lex V2 also uses this encoding\.

Each message consists of two sections: the prelude and the data\. The prelude section contains the total byte length of the message and the combined byte length of all of the headers\. The data section contains the headers and a payload\.

Each section ends with a 4\-byte big\-endian integer CRC checksum\. The message CRC checksum is for both the prelude section and the data section\. Amazon Lex V2 uses CRC32 \(often referred to as GZIP CRC32\) to calculate both CRCs\. For more information about CRC32, see [https://www.ietf.org/rfc/rfc1952.txt](https://www.ietf.org/rfc/rfc1952.txt)\.

Total message overhead, including the prelude and both checksums, is 16 bytes\.

The following diagram shows the components that make up a message and a header\. There are multiple headers per message\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lexv2/latest/dg/images/frame-diagram-frame-overview.png)

Each message contains the following components:
+ **Prelude:** Always a fixed size of 8 bytes, two fields of 4 bytes each\.
  + *First 4 bytes:* The total byte\-length\. This is the big\-endian integer byte\-length of the entire message, including the 4\-byte length field itself\.
  + *Second 4 bytes:* The headers byte\-length\. This is the big\-endian integer byte\-length of the headers portion of the message, excluding the headers length field itself\.
+ **Prelude CRC:** The 4\-byte CRC checksum for the prelude portion of the message, excluding the CRC itself\. The prelude has a separate CRC from the message CRC to ensure that Amazon Lex V2 can detect corrupted byte\-length information immediately without causing errors such as buffer overruns\.
+ **Headers:** Metadata annotating the message, such as the message type, content type, and so on\. Messages have multiple headers\. Headers are key\-value pairs where the key is a UTF\-8 string\. Headers can appear in any order in the headers portion of the message and any given header can appear only once\. For the required header types, see the following sections\.
+ **Payload:** The audio or text content being sent to Amazon Lex\.
+ **Message CRC:** The 4\-byte CRC checksum from the start of the message to the start of the checksum\. That is, everything in the message except the CRC itself\.

Each header contains the following components\. There are multiple headers per frame\.
+ **Header name byte\-length:** The byte\-length of the header name\.
+ **Header name:** The name of the header indicating the header type\. For valid values, see the following frame descriptions\.
+ **Header value type:** An enumeration indicating the header value type\. 
+ **Value string byte length:** The byte\-length of the header value string\.
+ **Header value:** The value of the header string\. Valid values for this field depend on the type of header\. For valid values, see the following frame descriptions\.