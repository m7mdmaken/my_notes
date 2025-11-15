

                                         **API**
                          **Application Programming Interface**

GET=> get data from DB
POST=>Send Data  [like(email,pass) in body] to DB - create new entity in DB
PUT/PATCH=>Edit data all/some
DELETE=>Delete from DB

	                                                    C R U D
        Create                          Read                      Update                        Delete
         POST                            GET                    PUT/PATCH                    DELETE

  =======================================================

Headers ==> additional data sent with POST request

- Autharization : Bearer fgh90fghegdfghsdfh5s5h3t6
- Content-Type: application/  json or x-www-form-urlencoded or xml
- Accept: application/xml or ....
- Accept-Language: ar

=======================================================

#####Body ==> response from DB   
The actual content (JSON/XML/text), which contains the data you requested or an error message
 mostly [raw] some times form-data

{"name": "Ahmed", "email": "ahmed@example.com", "age": 25)

=======================================================

##### AuthorizationAuthorization

most used
basic auth   =>  email,pass
Bearer token => string of nums & letters
