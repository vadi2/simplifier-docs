.. _vonk_components_configclass:

Configuration classes
=====================

A configuration class is a static class with two public static methods having the signature as below, that can add services to the Vonk FHIR Server dependency injection system, and add middleware to the pipeline.

.. code-block:: csharp

   [VonkConfiguration (order: xyz)] //xyz is an integer
   public static class MyVonkConfiguration
   {
      public static void ConfigureServices(IServiceCollection services)
      {
         //add services here to the DI system of ASP.NET Core
      }

      public static void Configure(IApplicationBuilder builder)
      {
         //add middleware to the pipeline being built with the builder
      }
   }

As you may have noticed, the methods resemble those in an ASP.NET Core Startup class. That is exactly where they are ultimately called from. We'll explain each of the parts in more detail.

:VonkConfiguration: This is an attribute defined by Vonk (package Vonk.Core, namespace Vonk.Core.Pluggability). It tells Vonk to execute the methods in this configuration class.
   The ``order`` property determines where in the pipeline the middleware will be added. You can see the order of the components in the :ref:`log<vonk_components_log_pipeline>` at startup.
:MyVonkConfiguration: You can give the class any name you want, it will be recognized by Vonk through the attribute, not the classname. We do advise you to choose a name that actually describes what is configured.
   It is also better to have multiple smaller configuration classes than one monolith adding all your components, so you allow yourself to configure your components individually afterwards.
:ConfigureServices: The main requirements for this method are:

   * It is public static;
   * It has a first formal argument of type ``Microsoft.Extensions.DependencyInjection.IServiceCollection``;
   * It is the only method in this class matching the first two requirements.

   This also means that you can give it a different name.
   Beyond that, you may add formal arguments for services that you need during configuration. You can only use services that are available from the ASP.NET Core hosting process, not any services you have added yourself earlier. Usual services to request are:

   * IConfiguration  
   * IHostingEnvironment

   These services will be injected automatically by Vonk.
:Configure: The main requirements for this method are:

   * It is public static;
   * It has a first formal argument of type ``Microsoft.AspNetCore.Buider.IApplicationBuilder``;
   * It is the only method in this class matching the first two requirements.

   This also means that you can give it a different name.
   Beyond that, you may add formal arguments for services that you may need during configuration. Here you can use services that are available from the ASP.NET Core hosting process *and* any services you have added yourself earlier. For services in request scope please note that this method is not run in request scope.
   These services will be injected automatically by Vonk.

We provided an :ref:`example<vonk_components_landingpage>` of this: creating your own landing page.

