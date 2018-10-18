# ERD diagram
The diagram represents the table design for the Bookkeeping system back-end database.
This document provides accompanying explanation for the tables and design decisions.


## Tags
Tags will be used to mark runs and logs, to make them easier to search and filter for users.


## Logs
Subtypes of logs are implemented with Single Table Inheritance.
The columns belonging to the subtype are prefixed with the name of the subtype.


## EOR reasons
The subsystems for EOR reason are not the same set as the subsystems for the logs. 
For now, we're using an enum for EOR reason subsystems. 
We may create a separate table later if this proves to be necessary.


## Tags
The DB will automatically add tags for present detectors to each run.
This is technically redundant with the data in  DetectorsInRun, but having this info also in the tags tables will allow for more uniform searching/filtering. 
I see these as two separate subsystems with different goals. The detectors tables as the hard record keeping, whereas the tags are more user-oriented and flexible.


## History tables
Updating run quality will only be allowed through a stored procedure.
It will in one transaction:
- Copy the old quality from Runs to RunEorHistory
- Update the quality to the new one in Runs
The most recent run quality will thus always be found in the Runs table.


## Threads
Logs can be ordered into threads. 
The threading scheme of the original system was kept because it was considered a good compromise. It only keeps a reference to the parent and the root of the thread.
There are schemes that provide better performance for certain queries, but they're either quite complicated and much less space-efficient (closure tables) or sacrifice referential integrity. Since we'll generally only query entire threads anyway and let the client sort them into a tree, I think this approach is very sensible.


# Other notes
- Logs for gas teams can be a regular comment with a gas tag
- In general, data is never deleted. The only exceptions are the counters such as the bytes_[x] fields which are updated on the fly (to be determined which other fields are counters).


# Permissions
The system must be able to work independently from SAMS.