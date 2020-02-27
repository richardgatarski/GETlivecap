# GETlivecap v1.0.0
GETlivecap is a proposed standard for an open, simple, and effective method with which a *live stream production* can GET real-time captions from a *captions server* via HTTP requests.

Some live streaming applications are already capable of using externally sourced data of different types, including plain text marked up as XML. Typically these application use the HTTP GET request method to pull XML formatted data from a web server. This fact constitutes the foundation for GETlivecap.

A short demonstration of GETlivecap in use is available at https://youtu.be/ZlJcfdMFh7I.
## Captions
The *captioning* terminology is used here as a synonym to *subtitling*, even though the wider meanings of those two terms differ. One or more lines of plain text consitutes a *caption*. GETlivecap does not support how the text is formatted or where the captions are positioned. Typically the number of lines, and a maximum line length, are determined by the live streaming producer.

Here is an example of a caption with two lines of text:
>This is one line of text,  
>followed by a second line.
## Captions server
The captions server is here understood as a part of a cloud service. Its server formats real-time text into a caption which is delivered using one of the response data types specified by the GETlivecap standard.

The server constantly updates the caption based on an incoming text flow created by captioners. Captioners can be humans (compare [CART](https://en.wikipedia.org/wiki/Communication_access_real-time_translation) or [STTR](https://en.wikipedia.org/wiki/Speech-to-text_reporter)) or Automatic Speech Recognition applications (compare [ASR](https://en.wikipedia.org/wiki/Speech_recognition)).

It is beyond the GETlivecap standard to specify how the captioners hear the live event and how the real-time text is created. The most simple method might be that the captioners are at the event venue and uses a QWERTY keyboard to send text to the server.

A captions server may use GET parameters in the query string to control various functionality, although that is not specified by GETlivecap. Such parameters could for example be settings for user and event specifications, response type (RSS or XML), number of lines, line length, scrolling captions, etc.
## The live streaming production
The live streaming production is assumed to use applications like [vMix](https://www.vmix.com/), [Tricaster](https://www.newtek.com/tricaster/), [OBS](https://obsproject.com/sv), [Wirecast](https://www.telestream.net/wirecast/), [VidBlasterX](https://www.vidblasterx.com/), [Livestream producer](https://livestream.com/producer), etc. to stream its output live on the web. 

During the production captions are included in the video output, for example as open captions by overlaying them as a [lower third](https://en.wikipedia.org/wiki/Lower_third). Other applications may encode the captions into the live stream as closed captions. (For the difference between open/closed captioning, see  https://en.wikipedia.org/wiki/Closed_captioning#Terminology).

Because the text is generated in real-time, and no handshaking is taking place, the live streaming application needs to constantly repeat the request. Typically 5 times/second (every 200 ms).
## Pros and cons
GETlivecap's simplicity implies advantages as well as restrictions, including:
- Firewall friendly (no special port requirements)
- No or minimal development efforts required by the live streaming application
- No error checking (e.g. words may be lost due to a bad internet connection, although unlikely)
- Text formatting/positioning can not be controlled by the captioners

It is basically up to the live stream producer to decide how open captions are presented in the live stream. Some live streaming apps may wrap a single long line into multiple shorter ones. Other apps may require that the line separation follows how the caption is outlined by the captions server.
## Response data types
Note that the server must always return only one (1) caption. This is the caption which at every moment is to be included in the video. The caption needs only to be updated whenever new text is received from the captioners. 

A few live streaming applications are already capable of using externally sourced data of different types, including plain text marked up as XML.

| Application | Support | Note |
| ----------- | ----------- | ----------- |
| vMix | XML, RSS |
| Tricaster | RSS |
| OBS | - | plug-in could be developed |
| Wirecast | - | can sort of use RSS, but no refresh option |
| VidBlasterX | ? |  |
| Livestream producer | ? |  |


Basic XML is preferred because of its simplicity. RSS is included only because some live streaming applications do not support any other viable response type.

It is adviced that the encoding format is UTF-8 to support international characters. Note that five characters (<&>"') needs special treatment by the server generating the XML file (see the [XML FAQ Special characters](http://xml.silmaril.ie/specials.html)).

To facilitate interoperability it is adviced to include all the tags, and their names, as used in the examples below.
### Basic XML-file
This example is also available for testing purposes at https://www.westreamu.com/sandbox/GETlivecap-XML-test.xml (use XPATH: ```//caption``` for parsing)
```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<caption>
  <line1>This is one line of text</line1>
  <line2>followed by a second line.</line2>
</caption>
```
A multi-line caption should always contain the specified number of lines, even if some of them are empty. For example, here a three line caption, of which two lines are empty (i.e. contains a SPACE):
```xml
<caption>
  <line1>I'm just saying that</line1>
  <line2></line2>
  <line3></line3>
</caption>
```
### RSS-file
[RSS](http://www.rssboard.org/rss-specification) is a dialect of XML, developed for other uses. Therefore, most of its meta data is ignored here. Even so it is adviced to include the RSS-required channel elements (title, description, link) as the live streaming app may expect them. The content of those three elements may be arbitrary, although the link should be formatted as a url (in case the live streaming app parse it).

A couple of the live streaming apps that currently support RSS utilize at least the item elements *title*, *link*, *pubDate*, and *description*. Therefore GETlivecap specify that these four elements should be used to support up to four lines of text in a captions block.

The example below is also available for testing purposes at https://www.westreamu.com/sandbox/GETlivecap-RSS-test.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0">
  <channel>
    <title>An example of RSS formated as a captions block</title>
    <description>This is a demo feed.</description>
    <link>domain.com/whatever</link>
    <item>
       <title>Line one</title>
       <link>Line two</link>
       <pubDate>Line three</pubDate>
       <description>Line four</description>
    </item>
  </channel>
</rss>
```
A multi-line caption should always contain all item elements needed for the specified number of lines, even if some of them are empty. For example, here a three line caption, of which one line is empty (i.e. contains a SPACE):
```xml
<item>
    <title>I'm just saying that this looks</title>
    <link>like a fantastic</link>
    <pubDate></pubDate>
</item>
```
## License
GETlivecap and the data in this repo is available for use under a CC BY 4.0 license (https://creativecommons.org/licenses/by/4.0/). For attribution just mention somewhere that the source is  For attribution just mention somewhere that the source is https://github.com/richardgatarski/GETlivecap.
