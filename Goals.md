# Goals

-   Store Users

    -   Display Names.
    -   UserIds.
    -   Associate data with each other.

-   Store Data

    -   **Store a Unix Timestamp** associated with the user.
        -   Store a time stamp of when the user presses a button, alongside which user.

-   Retrieve Data

    -   Show all times a specific user has pressed a button.
    -   _Show a timeline of all button presses by all the users._

-   Host this locally
    -   **Supabase running on a Docker Container.**
    -   _Cloudflare Tunnel to the outside world._
    -   **Some sort of Node Server for hosting the web app itself.**

# FrontEnd

- [x] Button to press that logs time.
- [x] Write that time to the database.
- [x] Show a running log of all database records to the user.