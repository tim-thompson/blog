---
layout: post
title: Keeping things Small with Microservices, Flask and Bonsai Trees
---

Recently I have become quite interested in the art of bonsai trees. With just 2 young trees however, I am but a novice. But I love seeing the large, well maintained collections of more experienced collectors. Whilst browsing bonsai communities I realised there is no software for managing a collection of trees, watering and feeding schedules etc. So I took the opportunity to create something, and today I'd like to take you through a simple microservice created with Python, Flask, Marshmallow and SQLAlchemy. The service will run on AWS Lambda as part of a larger application later. But it showcases nicely a simple Flask REST API complete with tests.

The full project can be found on GitHub here - https://github.com/tim-thompson/bonsai-trees

For those unfamiliar with Flask, it is a microframework for Python that makes creating web servers a breeze. It is designed to be minimal and there is a large range of community made extensions available so you can pick and choose functionality.

It differs in this way from Django where many features such as user handling are built in. This is what makes Flask excellent for microservices. It's super lightweight and the API in this article is just 150 lines of code.

Those 150 lines of code cover database connection and modelling, input and response marshalling, error handling, routing of requests and database interaction. There are a further 200 lines of code for testing the application.

I also personally think it's very readable and easy to see what's going on throughout the application.

**Under Construction - this post is still being written, please stand by.**