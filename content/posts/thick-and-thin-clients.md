+++
title = 'Thick & very thin clients'
date = '2024-07-18'
draft = false
+++
Instead of synchronizing state between two programs across a network, like on 
the web, it would be easier to have only one program and move inputs and
outputs across the network. For example, instead of running a web front end in
your browser and a backend somewhere on a server, and have them communicate
over the network transacting state, you would only relay the input from the
user and the output from the program over the network. This frees you from
HTTP, JSON, local and remote state syncing, failed requests, a lot of code
duplication between front and back etc.

Why not desktop applications that run locally? Because you'd have the same issue
as web clients. You have to maintain a frontend and a backend, with the additional
annoyance that users have to install it, and you have to deal with updates which
is a pain. This is one of the great appeals of the web, among many others;
applications can be updated on the fly, and they can run on pretty much any device.

The approach I am proposing (which I call the **teletype** approach), merges
both frontend and backend into one. It effectively acts as a local program, it
just so happens that its inputs and outputs are sent over the network instead
of received or rendered locally.

As a side note, in reality, data is still being kept in more than one place,
since most programs make use of a database, and so the program has to
continually update state into the database and read state from it as well,
taking into account changes that might occur since the last read by another
writer and so on.

Anyway, to be more specific, the input would mainly be in the form of mouse and
keyboard events. Additionally, file uploads and streamed data such as camera or
voice feeds can also be sent. For the output, it could be from pixels to text
(terminal UI).  To achieve this, you would use some simple and thin client that
you write no code for, which takes all the user inputs, encodes them, and ships
them to your actual program in some protocol that takes care of things arriving
in order, retrying when failing, and so on. Equally, the output is sent from
your server using the same protocol, and it would be rendered in the client
without you having to worry about much.

Of course, this is not new. In fact, this was how computers were used in the
early days with teletypes and terminals hooked to a mainframe. And nowadays, it
is used in SSH and remote desktop protocols like RDP, VNC and Citrix.

Yet, the fact remains that having do deal with one less entity having its own
version of the data, and all the complexity that brings, is a big improvement.
Naturally, there are drawbacks. Mainly, responsiveness is not as good. You can
do things like local echo, which consists on rendering the typed text locally
instead of sending each keystroke to the application over the network and
sending it altogether when the user has finished. This requires the client
to be aware of when and where the user can input text. Mocking other things
like mouse movements can also be done to improve UX. However, if the network is
slow, using something as basic as a dropdown menu will yield a poor UX, since
everything will feel sluggish and slow. Another drawback is offline support.
This way of interacting with the application means you have to be online to use
it. Network drops can be handled by keeping the connection alive for some time,
but the requirement to have a functioning network connection still remains.

Also, most business users won't be familiar with terminals and SSH, but if made
simple and more graphical via libraries like curses and bubbletea, I suspect
users would find themselves at ease with these tools. Alternatively, if
more graphical support is needed, e.g. images and videos, traditional GUIs can
also be used. It is surprisingly easy to spin up a GUI with C# and MS Visual
Studio. There are other alternatives like QT, GTK, and others.

However, I believe this very thin client approach should be used more often,
especially for internal tools in business contexts that don't see a very
frequent use. Usually, small to medium sized tools that share data across all
users is needed. If these tools are used very frequently by the same users,
then it would be worth improving on the UX side and a web application might be
the better option (something like HTMX, other SSR tools, or even a simple
vanilla web app are great options). However, if this is not the case, then the
teletype approach will get the program faster into production, make it easier
to develop and very likely has less bugs. It is a tradeoff between UX and less
complexity.
