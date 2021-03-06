Getting Started with the UserService 
=====================================

**URLS**

* **Production:** http://userservice.tangentme.com
* **Staging:** http://staging.userservice.tangentme.com

Getting an auth token
----------------------
**Request**

**Curl**::

    curl -X POST http://staging.userservice.tangentme.com/api-token-auth/ --data "username=admin&password=test"

**Python**::

	import requests
	url = 'http://staging.userservice.tangentme.com/api-token-auth/'	
	r=requests.post(url, data={"username":"admin","password":"test"})
	r.content
	Out[8]: '{"token": "d477c40272cc6132183647e67f10a90ae12e9d86"}'

**Response**::

	{"token": "11ce1e89d7e1d23e78e5c922c0cbd24bb2457cec"}

You can use this token to make future authorized requests

**Angular**

Add constant to settings file::

	.constant("SERVICE_BASE_URI", "http://staging.userservice.tangentme.com")

Add login to ng-service::

	apiService.login = function (username, password) {
		var deferred = $q.defer();
		var url = SERVICE_BASE_URI + "/api-token-auth/";

		$http.post(url, {
			username: username, password: password
		}).success(function (response, status, headers, config) {
			if (response.token) {
				//Store the token for further calls
			}

			deferred.resolve(response, status, headers, config);
		}).error(function (response, status, headers, config) {
			deferred.reject(response, status, headers, config);
		});

		return true; //deferred.promise;
	};

Authorizing with an auth token
-------------------------------

To identify yourself in consequent requests, set the `Authorization` header like below

**CURL**::

    curl GET http://staging.userservice.tangentme.com/users/me/ -H 'Authorization: Token 11ce1e89d7e1d23e78e5c922c0cbd24bb2457cec'

**Python**::

	import requests
	headers = {
		'content-type': 'application/json', 
		'Authorization':'Token d477c40272cc6132183647e67f10a90ae12e9d86'
	}
	requests.get('http://staging.userservice.tangentme.com/users/me/', headers=headers)

**Response**::  

	{
		"id": 5, 
		"first_name": "", 
		"last_name": "", 
		"username": "a", 
		"email": "", 
		"is_staff": true, 
		"profile": {
			"contact_number": "", 
			"status_message": null, 
			"bio": null
		}, 
		"authentications": [], 
		"roles": []
	}

**Angular**::

	apiService.getMe = function (token) {

		var deferred = $q.defer();
		var url = SERVICE_BASE_URI + "/users/me/";

		$http.get(url, {
			headers: {'Authorization': 'Token ' + token}
		}).success(function (response, status, headers, config) {
		if (response) {
			//Handle the response
		}

		deferred.resolve(response, status, headers, config);
		}).error(function (response, status, headers, config) {
			deferred.reject(response, status, headers, config);
		});

		return true; //deferred.promise;
	};

**Possible Responses**:

* 200 OK
* 401 Authorization failed:
   * Invalid Token
   * Authentication credentials were not provided.

