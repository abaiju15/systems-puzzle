# Insight DevOps Engineering Systems Puzzle

## Table of Contents

1. [Introduction](README.md#introduction)
2. [Puzzle details](README.md#puzzle-details)
3. [Bug fixes](README.md#bug-fixes)

# Introduction

Imagine you're on an engineering team that is building an eCommerce site where users can buy and sell items (similar to Etsy or eBay). One of the developers on your team has put together a very simple prototype for a system that writes and reads to a database. The developer is using Postgres for the backend database, the Python Flask framework as an application server, and nginx as a web server. All of this is developed with the Docker Engine, and put together with Docker Compose.

Unfortunately, the developer is new to many of these tools, and is having a number of issues. The developer needs your help debugging the system and getting it to work properly.

# Puzzle details

The codebase included in this repo is nearly functional, but has a few bugs that are preventing it from working properly. The goal of this puzzle is to find these bugs and fix them. To do this, you'll have to familiarize yourself with the various technologies (Docker, nginx, Flask, and Postgres). You definitely don't have to be an expert on these, but you should know them well enough to understand what the problem is.

Assuming you have the Docker Engine and Docker Compose already installed, the developer said that the steps for running the system is to open a terminal, `cd` into this repo, and then enter these two commands:

    docker-compose up -d db
    docker-compose run --rm flaskapp /bin/bash -c "cd /opt/services/flaskapp/src && python -c  'import database; database.init_db()'"

This "bootstraps" the PostgreSQL database with the correct tables. After that you can run the whole system with:

    docker-compose up -d

At that point, the web application should be visible by going to `localhost:8080` in a web browser.

Once you've corrected the bugs and have the basic features working, commit the functional codebase to a new repo following the instructions below. As you debug the system, you should keep track of your thought process and what steps you took to solve the puzzle.

# Bug fixes

These are the following issues that I encountered while trying to setup the environment locally and how I went about fixing them:

- `localhost:8080` not showing any content:

   There was just a small issue with the docker-compose where the binding of the host machine to the exposed port was not      syntacticaly correct. In order to troubleshoot this, I just had to take a peek at how the mapping was setup in docker-      compose.yml and in the nginx conf file.

- Clicking the submit button in index.html re-directing to `localhost,localhost:8080/success` instead of to `localhost:8080/success`:

   This was a tricky one. I didn't have much experience with nginx before this weekend, so I had to look at the nuances of      how the proxying was handled within `conf.d/flaskapp.conf` directives. Going through the nginx docs and some quick lookup    of http redirect issues with nginx on the web, I saw one of the headers being reset within the `location / {}` block.

- `localhost:8080/success` displaying a blank list of items:

   I did some server-side console logging for this within the `success` method in `app.py`. The strigifying of the returned    query result from the postgres db was not being served correctly on the page. I could see that the db connection was        successful and the items were being returned to the server, but the response back to the client of `str(results)` was not    working out. I decided to jsonify this result instead to send back data in a more presentational way.
