# Goals

- Store Users
    - Display Names.
    - UserIds.
    - Associate data with each other.

- Store Data
    - **Store a Unix Timestamp** associated with the user.
        - Store a time stamp of when the user presses a button, alongside which user.

- Retrieve Data
    - Show all times a specific user has pressed a button.
    - *Show a timeline of all button presses by all the users.*

- Host this locally
    - **Supabase running on a Docker Container.**
    - *Cloudflare Tunnel to the outside world.*
    - **Some sort of Node Server for hosting the web app itself.**