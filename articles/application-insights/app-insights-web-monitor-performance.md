---
title: "Supervisar el estado y el uso de su aplicación con Application Insights"
description: "Introducción a Application Insights. Analice el uso, la disponibilidad y el rendimiento de sus aplicaciones web de Microsoft Azure o local."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: cbdef43381deac957c0e48b7043273c43b032935
ms.contentlocale: es-es
ms.lasthandoff: 04/07/2017


---
# <a name="monitor-performance-in-web-applications"></a>Supervisar el rendimiento de aplicaciones web


Asegúrese de que la aplicación tendrá un rendimiento correcto y descubra rápidamente los posibles errores. [Application Insights][start] le indicará las excepciones y los problemas de rendimiento, y lo ayudará a buscar y diagnosticar las causas principales.

Application Insights puede supervisar aplicaciones y servicios web Java y ASP.NET y servicios WCF. Pueden estar hospedados localmente, en máquinas virtuales o como sitios web de Microsoft Azure. 

En el lado del cliente, Application Insights puede obtener datos de telemetría de páginas web y una amplia variedad de dispositivos, entre los que se incluyen iOS, Android y aplicaciones de la Tienda Windows.

## <a name="setup"></a>Configuración de la supervisión de rendimiento
Si todavía no ha agregado Application Insights a un proyecto (es decir, si no dispone de ApplicationInsights.config), puede comenzar con uno de estos procedimientos:

* [Aplicaciones web ASP.NET](app-insights-asp-net.md)
  * [Agregar supervisión de excepciones](app-insights-asp-net-exceptions.md)
  * [Agregar supervisión de dependencias](app-insights-monitor-performance-live-website-now.md)
* [Aplicaciones web J2EE](app-insights-java-get-started.md)
  * [Agregar supervisión de dependencias](app-insights-java-agent.md)

## <a name="view"></a>Exploración de las métricas de rendimiento
En el [Portal de Azure](https://portal.azure.com), busque el recurso de Application Insights que configuró para la aplicación. La hoja de información general muestra los datos de rendimiento básicos:

Haga clic en cualquier gráfico para ver su contenido con mayor detalle y los resultados durante más tiempo. Por ejemplo, haga clic en el mosaico Solicitudes y seleccione un intervalo de tiempo:

![Haga clic en las distintas opciones para obtener más datos y seleccionar un intervalo de tiempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Haga clic en un gráfico para elegir las métricas mostradas o para agregar un nuevo gráfico y seleccionar sus métricas:

![Haga clic en un gráfico para elegir las métricas](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desactive todas las métricas** para ver todas las métricas disponibles. Las métricas se organizan en grupos. Cuando se selecciona un miembro de un grupo, solo aparecen los demás miembros de dicho grupo.
> 
> 

## <a name="metrics"></a>¿Qué significa todo esto? Informes y mosaicos de rendimiento
Puede obtener una gran variedad de métricas de rendimiento. Comencemos por las que aparecen de forma predeterminada en la hoja de la aplicación.

### <a name="requests"></a>Solicitudes
El número de solicitudes HTTP que se reciben en un período específico. Compare este dato con los resultados de otros informes para ver cómo se comporta la aplicación cuando la carga varía.

Las solicitudes HTTP incluyen todas las solicitudes GET o POST de páginas, datos e imágenes.

Haga clic en el mosaico para obtener las cuentas de URL específicas.

### <a name="average-response-time"></a>Tiempo de respuesta promedio
Mide el tiempo entre la entrada de una solicitud web y la devolución de la respuesta correspondiente.

Los puntos muestran una media móvil. Si se producen muchas solicitudes, es posible que algunas de ellas se desvíen de la media sin que en el gráfico se muestre ningún pico o descenso evidente.

Busque picos poco habituales. Por lo general, el tiempo de respuesta aumenta cuando aumentan las solicitudes. Si este aumento es desproporcionado, la aplicación puede alcanzar el límite de recursos (por ejemplo, de CPU o de un servicio que esté usando).

Haga clic en el mosaico para obtener los tiempos de URL específicas.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Solicitudes más lentas
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Muestra las solicitudes que pueden precisar un ajuste de rendimiento.

### <a name="failed-requests"></a>Error en las solicitudes
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Un recuento de las solicitudes que inician excepciones no detectadas.

Haga clic en el mosaico para ver los detalles de errores específicos y seleccione una solicitud individual para revisarla de forma más exhaustiva. 

Solo se conserva una muestra representativa de los errores para su inspección individual.

### <a name="other-metrics"></a>Otras métricas
Si desea ver las demás métricas que puede mostrar, haga clic en un gráfico y desactive todas las métricas para ver el conjunto completo de opciones disponibles. Haga clic en (i) para ver la definición de cada métrica.

![Cancele todas las métricas para ver el conjunto completo](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Si selecciona una métrica, se deshabilitarán las demás que no pueden aparecer en el mismo gráfico.

## <a name="set-alerts"></a>Establecer alertas
Para recibir notificaciones por correo electrónico de los valores no habituales de cualquier métrica, agregue una alerta. Puede decidir si se debe enviar un mensaje de correo electrónico a los administradores de cuentas o a direcciones de correo electrónico específicas.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Establezca el recurso antes de las demás propiedades. No elija los recursos de la prueba web si desea establecer alertas en las métricas de rendimiento o de uso.

Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.

*No puedo ver el botón Agregar alerta* : ¿es una cuenta de grupo a la que tiene acceso de solo lectura? Consulte con el administrador de cuenta.

## <a name="diagnosis"></a>Diagnóstico de problemas
Para buscar y diagnosticar problemas de rendimiento, lea estas sugerencias:

* Configure las [pruebas web][availability] para recibir una alerta si el sitio web deja de funcionar o si no responde correctamente, o lo hace más lentamente de lo habitual. 
* Compare el recuento de Solicitudes con otras métricas para ver si la lentitud de respuesta o los errores se encuentran relacionados con la carga.
* [Inserte y busque instrucciones de seguimiento][diagnostic] en el código para facilitar la identificación de los problemas.

## <a name="next"></a>Pasos siguientes
[Pruebas web][availability]: reciba solicitudes web en su aplicación de forma periódica desde distintos lugares del mundo.

[Capture y busque seguimientos de diagnóstico][diagnostic]: inserte llamadas de seguimiento y cribe los resultados para identificar los problemas.

[Seguimiento del uso][usage]: averigüe cuántas personas usan la aplicación.

[Solución de problemas][qna]: con preguntas y respuestas



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md



