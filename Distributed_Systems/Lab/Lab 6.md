
- build a server and client that cache extents at the client
- The main challenge is to ensure consistency of extents cached at different clients.


Steps:
- Let the extent_client inherit lock_release_user
- Implement lock_release_user functions in the extent_client
	- Implement the state along with the dirty_flag and remove_flag
	- Modify the get, put, remove functionalities