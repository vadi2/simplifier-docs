.. _feature_customresources:

Custom Resources
================

Custom Resources are not formally defined in the FHIR Specification. To Vonk a Custom Resource is a resource with a definition that is a specialization of DomainResource, but that is not in the Core FHIR Specification. Vonk can handle these, provided it knows about the StructureDefinition that defines it. This page explains how to register such a StructureDefinition and store custom resources.

.. warning::

   Custom Resources are not interoperable. Do not use them for exchanging resources outside your own programming control boundaries.

What to use them for?
---------------------

Vonk can be used as a platform to build apps on. In these apps, structures arise outside of the FHIR Specification or even the Health domain. Still, it would be useful to also use Vonk to store, search and version these structures. Note that this is only for internal use in the app.

Register the definition
-----------------------

Just like any resourcetype, the definition for a custom resource is formalized as a StructureDefinition. Vonk will recognize it as the definition of a custom resourcetype if and only if:

* base = DomainResource
* derivation = specialization
* kind = resource
  
This also means that a Logical Model as-is cannot be used as the definition of a custom resourcetype.

Examples of these can be found in the specification: each resourcetype is defined this way. The easiest way to get started is with the definition of Basic (in `xml <https://www.hl7.org/fhir/STU3/basic.profile.xml.html>`_ or `json <https://www.hl7.org/fhir/STU3/basic.profile.json.html>`_, and adjust that:

#. Choose a name for the type, let's say 'Foo'.
#. Choose a url for the type. In STU3 this has to start with http://hl7.org/fhir/StructureDefinition/ (constraint sdf-7), so http://hl7.org/fhir/StructureDefinition/Foo makes sense.
#. Make sure the id, name and type elements align with the name 'Foo'.
#. Adjust the description
#. Make sure all the elements in the differential start with 'Foo.' 
#. (Recommended) Store your definition in Simplifier.net for version management, comments and collaboration.

If you have created the StructureDefinition, register it in Vonk using any of the methods mentioned in :ref:`conformance`. As an example we will issue an update interaction on the Administration API::

   ``PUT <base-url>/administration/StructureDefinition/Foo``

By using an update we can choose the id and hence the location of this StructureDefinition. Vonk does this by default for all the resourcetypes defined by the specification as well.

Use a resource
--------------

To test whether you can actually use the endpoint associated with your custom resourcetype, try a search on it: ``GET <base-url>/Foo``. This should return an empty bundle. If you get an OperationOutcome with the text "Request for not-supported ResourceType(s) Foo", the registration of the definition is not correct.

.. note::

   The CapabilityStatement will not list the custom definition. This is because the element CapabilityStatement.rest.resource.type has a Required binding to the ResourceType valueset. And obviously this valueset does not contain our 'Foo' resourcetype.

Now use your favorite editor to create a resource that conforms to the Foo StructureDefinition. And then create it on Vonk: ``POST <base-url>/Foo``.

All the operations on specification-defined resourcetypes are also available for custom resources. You can also use them in a batch or transaction bundle. There is one limitation however:

* Validation: Custom Resources can not be validated. This implies that :ref:`feature_prevalidation` cannot be used in conjunction with Custom Resources.

We expect to remove this limitation in a future version of Vonk.