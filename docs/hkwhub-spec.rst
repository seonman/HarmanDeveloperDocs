HKWHub Specification
==================================

REST API Specification
-----------------------

Session Management
~~~~~~~~~~~~~~~~~~~~

Start Session
^^^^^^^^^^^^^^

- API: GET /v1/init_session
- Response
	- Returns a unique session id
	- The session id will be used for upcoming requests.
- Example:
	- Request: ``http://192.168.1.10/v1/init_session``
	- Response: 

.. code-block:: json

	{"SessionID" : "1000"}

