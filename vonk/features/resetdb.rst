.. _feature_resetdb:

Reset the database
==================

If you have set up Vonk as a reference server in a testing environment, it can be useful to reset the database.
You would usually do this in combination with :ref:`feature_preload`.

To reset the database, execute:
::

    POST http(s)://<vonk-endpoint>/administration/reset

Vonk will return statuscode 200 if the operation succeeded. 

If you are :ref:`not permitted <configure_administration_access>` to perform the reset, Vonk will return statuscode 403.

.. note:: On a large database this operation may take a while.

An alternative, if you have direct access to the database server, is to delete the database altogether and have Vonk recreate it again.

* If you run on SQL Server, see :ref:`configure_sql` for the ``AutoUpdateDatabase`` feature. 
* If you run on MongoDB, Vonk will recreate the collection by default if it is not present.

Although the operation requires no further arguments, it requires a POST, since it is certainly not side-effect free.
