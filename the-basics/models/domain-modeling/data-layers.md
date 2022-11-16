# Data Layers

![](https://github.com/ortus-docs/coldbox-docs/tree/97b8636ca1e8f4651f1021343c097bb3a7c2e9b9/.gitbook/assets/MVC%2Bobjects.png)

If I know that my database operations will get very complex or I want to further add separation of concerns, I could add a third class to the mix: `BookGateway.cfc` or `BookDAO.cfc` that could act as my data gateway object.

Now, there are so many design considerations, architectural requirements and even types of service layer approaches that I cannot go over them and present them. My preference is to create service layers according to my application's functionality (Encompasses 1 or more persistence tables) and create gateways or data layers when needed, especially when not using an ORM. The important aspect here, is that I am thinking about my project's OO design and not how I will persist the data in a database. This, to me, is key!

Understanding that I am more concerned with my object's behavior than how will I persist their data will make you concentrate your efforts on the business rules, model design and less about how the data will persist. Don't get me wrong, persistence takes part in the design, but it should not drive it.
