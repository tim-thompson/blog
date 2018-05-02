---
layout: post
title: Keeping things Small with Microservices, Flask and Bonsai Trees
---

I enjoy a range of hobbies, but one of my more recent interests is Bonsai Trees. Though I am but a novice with just 2 young trees, I love seeing the large, well maintained collections of more experienced collectors. Whilst browsing I realised there is a complete lack of any good software for managing a collection of trees and the schedules etc. required for maintenance. So I took the opportunity to create something, and today I'd like to take you through a simple microservice created with Python, Flask and SQLAlchemy. The service is an example of what will run on AWS Lambda as part of a larger application later. But it showcases nicely a simple Flask REST API complete with tests.