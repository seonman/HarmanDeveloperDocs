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

.. code-block:: json

	{"SessionID" : "1000"}

