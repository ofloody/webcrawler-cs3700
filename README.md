# High Level Implementation

Our high level implementation works like this:

- Send an initial GET request for the login page
- Parse the HTML and header for the CSRF cookie and CSRF middleware token
- Send a POST request for our login and with the credentials provided in the command line
- When we receive a 302, we gather the next location, and add it as our initial page_to_visit (our frontier)
- Then, we perform a BFS on the website.
- While we have not found 5 secret flags or the queue is empty:
  - attempt to fetch it with a GET. If it fails, attempt to reopen the socket and try again
  - If it is successfully fetched, check the status code. If it's a 500, readd it to the queue
  - If it's a 302, add it's redirect location to the queue and add the original to the visited pages list
  - If it's a 200, add this page to the visited pagese list. Then, parse it for secret flags, If one is found, add to the secret flag list. Then parse it for offsite links. If the links start with fakebook and we have not visited them, add them to the queue.
  - In addition, refresh the cookies from the header of the results.
- Finally, print all the secret flags found.

# Challenges

The main challenge we faced was figuring out how to format the http requests. We figured out the algorithm for traversal relatively quickly, and also implemented connection: stay alive and reopening the socket. We didn't have too much trouble with chunking either. However, we were having issues with the inital post reqeust. A large part of us overcoming this was reviewing what cookies and permission were needed. Once we realized that we were missing things, we implemented continously grabbing the cookies from the header and updating them, as well as the initial get request needed for cookies for the front page.

We also had some issues with consitntly finding all flags. However, we realized the reason for this was because we were not handling 500's and 302's, and so a large part of the website was randomly being cut off from our graph search. Once we handled resending and redirects, we were able to consistently get all 5 flags.

# Testing

Our main testing was through printing. Before we removed them for for the final submission, we had a careful logging system soo we could consistently keep track of the what page/count the program was at, when it encountered 500/302's, when it needed to reopen the socket, when it found a secret flag, etc. We used this to debug and it made it much easier to understand what and how our crawler was performing.
