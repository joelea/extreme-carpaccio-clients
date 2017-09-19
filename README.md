# Instructions for participants

## Technical Instructions

### Setup
1. The facilitator will start up a central server and will display this server's URL.
2. To be able to play, you will need to set up a client that can connect with the central server. To obtain a client, you will set up an HTTP server on your local machine. Note that your local HTTP _server_ functions as the _client_ to the central server.
3. To set up your local server, we provide a number of HTTP servers in this repository - simply clone, pick the server written in the language you are most comfortable with, and start up that server. Otherwise, you can create your own HTTP server. You **don't have to install any HTTP server** like Tomcat, Apache, ngnix, etc.
4. Once you have set up your local server, find your local computer's IP address.
5. In your browser of choice, navigate to `http://<ip_address>:<port>/ping`.
  - Note that the value of `port` depends on the server's language. You should look at the server's code to figure out what your port will be.
6. The `ping` endpoint should return the text `pong`. If you see this, your local server is running!
7. You now need to register your local server as a client to the central server. Go to the central server's URL and register your local server. **Note that the URL you register must be of the form `http://<ip_address>:<port>`.**
8. Ensure that your registration shows up on the scoreboard without an `!` next to this - the absence of the `!` indicates that the central server is able to talk with your local server without any issues.

### Play
1. The central server will start sending orders to your local client like this:

    ```
    POST /order HTTP/1.1
    {
        "prices": [65.6,27.26,32.68],
        "quantities": [6,8,10],
        "country": "IE",
        "reduction":"STANDARD"
    }
    ```

2. Your job is to calculate the amount of the received orders and answer with a JSON object bill, i.e.: `{ "total": 938.48 }`. Note that the server checks responses using two decimal digits of precision - so `10.1234` and `10.12` are equal.
3. Your score will be shown on the dashboard.
4. The server will send you feedback based on what you have responded. So check if your local HTTP server already handles `POST /feedback`. **If your server does not handle this, you must implement it; otherwise you will not be able to figure out what is going on with your responses**. Here is an example of a feedback the central server can send to you:

    ```
    POST /feedback HTTP/1.1
    {
        "type": "ERROR",
        "content": "The field \"total\" in the response is missing."
    }
    ```

## Game rules

If your answer match the expected bill, then congratulations: you earned the amount of the bill!

If you answer a total that does not match the expected bill, you will be charged with 50% of the amount of the right bill.

Your answer will be ignored if one of these conditions are met:
- your server is unreachable
- you responded with HTTP code 404

## Functional Instructions

To calculate the bill, you need to consider the tax of the country from which the order came from and the reduction.

### Taxes
Here is the tax table used in the exercise:

*Country* | *Code* | *Tax*
--- | --- | ---
Germany | DE | 20%
United Kingdom | UK | 21%
France | FR | 20%
Italy | IT | 25%
Spain | ES | 19%
Poland | PL | 21%
Romania | RO | 20%
Netherlands | NL | 20%
Belgium | BE | 24%
Greece | EL | 20%
Czech Republic | CZ | 19%
Portugal | PT | 23%
Hungary | HU | 27%
Sweden | SE | 23%
Austria | AT | 22%
Bulgaria | BG | 21%
Denmark | DK | 21%
Finland | FI | 17%
Slovakia | SK | 18%
Ireland | IE | 21%
Croatia | HR | 23%
Lithuania | LT | 23%
Slovenia | SI | 24%
Latvia | LV | 20%
Estonia | EE | 22%
Cyprus | CY | 21%
Luxembourg | LU | 25%
Malta | MT | 20%

For example, given the order:
  ```
  {
    "prices":[15.99],
    "quantities":[1],
    "country":"ES",
    "reduction":"STANDARD"
  }
  ```

the response should be `{"total":19.03}`.

### Reductions
Most orders will have a reduction type of `STANDARD`. The standard reductions are listed here:

*Total* | *Reduction*
--- | ---
\>= 50 000 EUR | 15 %
\>= 10 000 EUR | 10 %
\>= 7 000 EUR | 7 %
\>= 5 000 EUR | 5 %
\>= 1 000 EUR | 3 %

For example, given the order:
  ```
  {
    "prices":[4.1,8.03,86.83,65.62,44.82],
    "quantities":[10,3,5,4,5],
    "country":"AT",
    "reduction":"STANDARD"
  }
  ```

the response should be `{"total":1166.62}`.

Note that:

1. reductions are applied *after* the taxes;
2. **other reduction types can appear during the game**. You need to pay attention to the server's feedback to figure out how reductions change.
