.. _vonk_components_plugintemplate:

Template for a plugin
=====================

.. attention::

   A complete template for a Vonk plugin can be found on `Github     <https://github.com/FirelyTeam/Vonk.Plugin.ExampleOperation>`_. 
   It covers all details on how to create a custom operation and how to use Vonk services internally.

A regular Vonk plugin acts on the IVonkContext and its IVonkRequest and IVonkResponse properties.

You don't have to create ASP.NET Core middleware yourself. You just need to create a service acting on IVonkContext. In the configuration you can specify when the service should be called, and in which position in the pipeline it should be put. See :ref:`components_interactionhandler` for details on that.

You can use the following code as a template for a plugin:

.. code-block:: csharp

   using Microsoft.AspNetCore.Builder;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.DependencyInjection.Extensions;
   using System.Threading.Tasks;
   using Vonk.Core.Context;
   using Vonk.Core.Context.Features;
   using Vonk.Core.Pluggability;
   using Vonk.Fhir.R3;
   using F = Hl7.Fhir.Model;

   namespace com.mycompany.vonk.myplugin
   {

      public class MyPluginService
      {
         public async Task Act(IVonkContext vonkContext)
         {
            var (request, args, response) = vonkContext.Parts();
            //do something with the request
            //write something to the response
            response.Payload = new F.Patient{Id = "pat1"}.ToIResource();
            response.HttpResult = 200;
         }
      }

      [VonkConfiguration(order: 5000)]
      public static class MyPluginConfiguration
      {
         public static IServiceCollection AddMyPluginServices(IServiceCollection services)
         {
            services.TryAddScoped<MyPluginService>();
            return services;
         }

         public static IApplicationBuilder UseMyPlugin(IApplicationBuilder app)
         {
            app.OnCustomInteraction(VonkInteraction.system_custom, "myOperation").HandleAsyncWith<MyPluginService>((svc, context) => svc.Act(context));
            return app;
         }
      }
   }
