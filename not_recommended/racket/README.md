# Extreme Carpaccio Racket starting code

## Prerequisites

You will need [Racket](http://racket-lang.org/)  installed.

## Test Driven Development

1. Lauch DrRacket
2. Load file ```carpaccio.rkt ```
3. Run it with ```Ctrl-R```
4. Check tests are ok with message ```all test run```

You can start your TDD loops.

## Running in "production"

To start a web server for the application, run:

    $ racket -tm web-server.rkt
	
You can save the output in a log file with the ```tee``` command:

    $ racket -tm web-server.rkt | tee -a log.txt

