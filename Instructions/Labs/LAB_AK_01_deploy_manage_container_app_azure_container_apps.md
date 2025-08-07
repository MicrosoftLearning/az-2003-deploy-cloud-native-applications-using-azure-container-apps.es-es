---
lab:
  title: 'Laboratorio: Implementar y administrar una Container App mediante Azure Container Apps'
  type: Answer Key
  module: 'Module 6: Deploy and manage a container app using Azure Container Apps'
---

# Laboratorio: Implementar y administrar una Container App mediante Azure Container Apps
# Clave de respuesta del laboratorio de alumnos

## Instrucciones

En este laboratorio, implementará y administrará una aplicación mediante Azure Container Apps. Para implementar la solución, empiece por configurar un entorno de desarrollo que use una combinación de herramientas locales y recursos de Azure. Una vez preparado el entorno, use Azure Container Registry, Azure Container Apps y Azure Pipelines para implementar y administrar la aplicación.  

Al final de este laboratorio, podrá realizar lo siguiente:

1. Configurar una conexión segura entre una instancia de Azure Container Registry y una instancia de Azure Container Apps.
1. Crear y configurar una aplicación de contenedor en Azure Container Apps.
1. Configurar la integración continua mediante Azure Pipelines.
1. Escalar una aplicación implementada en Azure Container Apps.
1. Administrar revisiones en Azure Container Apps.

### Ejercicio 1: Configurar recursos de Azure

En este ejercicio, configurará los recursos de Azure que admiten la solución de Azure Container Apps.

Tardará aproximadamente entre 10 y 15 minutos en completar las siguientes tareas:

- Examinar la configuración del grupo de recursos.
- Configurar una instancia de Azure Virtual Network con subredes.
- Configurar un recurso de Azure Service Bus.
- Configurar un recurso de Azure Container Registry.

#### Tarea 1: Examinar la configuración del grupo de recursos

Complete los pasos siguientes para examinar el valor de ubicación del grupo de recursos que se usa en este laboratorio.

1. En el entorno de laboratorio, abra una ventana del explorador y vaya a Azure Portal: `https://portal.azure.com/`

    Trabaje con el instructor educativo si necesita ayuda para iniciar sesión en Azure Portal con una suscripción o cuenta adecuada para este laboratorio.

1. En la página principal de Azure Portal, en **Navegar**, seleccione**Grupos de recursos**.

1. En la página Grupos de recursos, seleccione **RG1**.

    Si no se ha creado el grupo de recursos RG1, créelo ahora.

1. Anote el valor de **Ubicación** asignado al grupo de recursos **RG1**.

    Usará la misma ubicación o región al crear otros recursos de Azure durante este laboratorio.

1. Cierre la página **RG1** y, a continuación, cierre la página **Grupos de recursos**.

#### Tarea 2: Crear una red virtual y subredes

Complete los pasos siguientes para configurar una red virtual y subredes.

1. En la barra de búsqueda superior de Azure Portal, en el cuadro de texto Buscar, escriba **red virtual**.

1. En los resultados de la búsqueda, seleccione **Redes virtuales**.

1. Seleccione **Creación de una red virtual**.

1. En la pestaña Aspectos básicos, configure la red virtual de la siguiente manera:

    - Suscripción: Asegúrese de que está seleccionada la suscripción de Azure que usa para este proyecto guiado.
    - Nombre del grupo de recursos: Seleccione **RG1**.
    - Nombre de la red virtual: Escriba **VNET1**.
    - Región: Asegúrese de que la región especificada coincida con el valor de ubicación del grupo de recursos.

1. Seleccione la pestaña **Direcciones IP**.

1. En la pestaña Direcciones IP, en **Subredes**, seleccione **predeterminado**.

1. En la página Editar subred, configure la subred de la siguiente manera:

    - Nombre: escriba **`PESubnet`**.
    - Dirección inicial: Asegúrese de que se especifica **10.0.0.0**.
    - Tamaño de la subred: Asegúrese de que se especifica **/24 (256 direcciones)**.

    Esta primera subred se usará para un punto de conexión privado (Azure Container Registry).

1. Seleccione **Guardar**.

1. En la pestaña Direcciones IP, seleccione **+ Agregar una subred**.

1. En la página Agregar una subred, configure la subred de la siguiente manera:

    - Nombre: escriba **`ACASubnet`**.
    - Dirección inicial: Asegúrese de que se especifica **10.0.4.0**.
    - Tamaño de la subred: Asegúrese de que se especifica **/23 (512 direcciones)**.

    La subred de Azure Container Apps requiere un espacio de direcciones superior a 256.

1. Seleccione **Agregar**.

1. Seleccione **Revisar + crear**.

1. Una vez pasada la validación, seleccione **Crear**.

1. Espere a que se complete la implementación y cierre la página de VNET1.

#### Tarea 3: Configurar Service Bus

Complete los pasos siguientes para configurar una instancia de Service Bus.

1. En la barra de búsqueda superior de Azure Portal, en el cuadro de búsqueda, escriba **service bus**.

1. En los resultados de la búsqueda, seleccione **Service Bus**.

1. Seleccione **Crear el espacio de nombres de Service Bus**.

1. En la pestaña Aspectos básicos, configure el espacio de nombres de Service Bus de la siguiente manera:

    - Suscripción: Asegúrese de que está seleccionada la suscripción de Azure que usa para este proyecto guiado.
    - Nombre del grupo de recursos: Seleccione **RG1**.
    - Nombre del espacio de nombres: Escriba **sb-az2003-** seguido de sus iniciales y la fecha. Por ejemplo: **sb-az2003-cah12oct**.
    - Ubicación: Asegúrese de que la ubicación especificada coincida con el valor de ubicación del grupo de recursos.
    - Plan de tarifa: Seleccione **Básica**.

1. Seleccione **Revisar + crear**.

1. Una vez que aparezca el mensaje Validación correcta, seleccione **Crear**.

1. Espere a que se complete la implementación y cierre la página Espacio de nombres de Service Bus.

#### Tarea 4: Configurar Azure Container Registry

Complete los pasos siguientes para configurar una instancia de Container Registry.

1. En la barra de búsqueda superior de Azure Portal, en el cuadro de búsqueda, escriba **registro de contenedor**.

1. En los resultados de la búsqueda, seleccione **Registros de contenedor**.

1. En la página Registros de contenedor, seleccione **Crear registro de contenedor** o **+ Crear**.

1. En la pestaña Aspectos básicos de la página Crear registro de contenedor, especifique la siguiente información:

    > [!NOTE]
    > El nombre del registro debe ser único. Además, hace falta el nivel Premium para un vínculo privado con puntos de conexión privados.

    - Suscripción: Asegúrese de que está seleccionada la suscripción de Azure que usa para este proyecto guiado.
    - Grupo de recursos: Seleccione **RG1**.
    - Nombre de registro: Escriba **acraz2003** seguido de sus iniciales y la fecha. Por ejemplo: **acraz2003cah12oct**
    - Ubicación: Asegúrese de que la ubicación especificada coincida con el valor de ubicación del grupo de recursos.
    - SKU: Seleccione **Premium**.

1. Seleccione **Revisar + crear**.

1. Cuando aparezca el mensaje Validación superada, seleccione **Crear**.

1. Una vez completada la implementación, abra el recurso de Registro de contenedor.

1. En el menú de la izquierda de la página Registro de contenedor, en **Configuración**, seleccione **Redes**.

1. En la página Redes, en la pestaña Acceso público, asegúrese de que está seleccionada la opción **Todas las redes**.

1. En el menú de la izquierda, en **Configuración**, seleccione **Propiedades**.

1. En la página Propiedades, seleccione **Usuario administrador** y, a continuación, seleccione **Guardar**.

1. Cierre la página Registro de contenedor.

1. Cierre la ventana de Azure Portal (explorador).

### Ejercicio 2: Configurar las herramientas de desarrollo en el entorno de host

En este ejercicio, se garantiza que el scripting y las herramientas de desarrollo estén configuradas correctamente en la máquina virtual.

Tardará aproximadamente entre 20 y 25 minutos en completar las siguientes tareas:

- Configurar extensiones de la CLI de Azure.
- Instale Docker para escritorio.
- Instalar el SDK de .NET 8.
- Actualizar Visual Studio Code y configurar extensiones.

#### Tarea 1: Desinstalar Visual Studio Code

Complete los pasos siguientes para desinstalar Visual Studio Code.

1. Abra el menú Inicio de Windows.

1. En el menú Inicio, seleccione **Configuración**.

1. En el menú del lado izquierdo, seleccione **Aplicaciones** y, después, **Aplicaciones instaladas**.

1. Localice **Microsoft Visual Studio Code**.

1. A la derecha de Microsoft Visual Studio Code, seleccione los puntos suspensivos (...) y, a continuación, seleccione **Desinstalar**.

1. En la ventana emergente, seleccione **Desinstalar**.

1. Cuando se le solicite, seleccione **Sí** y, a continuación, seleccione **Aceptar**.

1. Cierre la página Configuración.

#### Tarea 2: Configurar extensiones de la CLI de Azure

Complete los siguientes pasos para configurar la CLI de Azure.

1. Abra una aplicación de terminal o línea de comandos, como el Símbolo del sistema de Windows.

1. Inicie sesión en Azure con el comando `az login`.

    Se abrirá una ventana del explorador que le permite seleccionar la cuenta de Azure.

1. Siga las indicaciones para completar el proceso de autenticación.

1. Cierre la ventana del explorador.

1. En la aplicación de línea de comandos, para instalar la extensión de Azure Container Apps, escriba el siguiente comando: `az extension add --name containerapp --upgrade`

1. Cierre la aplicación de línea de comandos.

#### Tarea 3: Instalar Docker Desktop

Complete los pasos siguientes para instalar Docker Desktop.

1. Abra una ventana del explorador y vaya a la página de instalación de Docker Desktop: `https://docs.docker.com/desktop/install/windows-install/`

1. Seleccione **Docker Desktop para Windows** y espere a que se descargue el archivo del instalador.

1. Abra el archivo del instalador descargado y siga las instrucciones en línea para instalar Docker Desktop.

    El proceso de instalación tarda aproximadamente 5 minutos.

1. Una vez completada la instalación, seleccione **Cerrar y reiniciar**.

1. Cuando se reinicie la máquina virtual actualizada, espere a que aparezca la ventana **Acuerdo de servicio de suscripción de Docker**.

1. En la página Acuerdo de servicio de suscripción de Docker, seleccione **Aceptar**.

1. En la página Finalizar configuración de Docker Desktop, seleccione **Finalizar**.

1. En la página Control de cuentas de usuario, seleccione **Sí**.

1. En la página Bienvenido a Docker Desktop, seleccione **Continuar sin iniciar sesión**.

1. En la página Información sobre el trabajo que realiza, seleccione **Omitir**.

1. Espere a que se complete el proceso de inicio del motor de Docker y, a continuación, minimice la aplicación Docker Desktop.

    No cierre Docker Desktop, solo tiene que minimizar la aplicación en ejecución.

#### Tarea 4: Instalar el SDK de .NET 8

Complete los pasos siguientes para instalar el SDK de .NET 8.

1. Abra una ventana del explorador web y vaya a la página de descarga del SDK de .NET 8: `https://dotnet.microsoft.com/download`

1. Seleccione **SDK de .NET x64**

1. Una vez completada la descarga, abra el archivo de instalación y siga las instrucciones en línea para instalar el SDK de .NET 8.

1. Cierre la ventana del explorador.

#### Tarea 5: Configurar Visual Studio Code con las extensiones de C#, Docker y Azure App Service

Complete los pasos siguientes para configurar Visual Studio Code con extensiones.

1. Abra una ventana del explorador web y vaya a la página de descarga de Visual Studio Code: `https://code.visualstudio.com/download`

1. Seleccione **Windows** y espere a que se descargue el archivo del instalador.

1. Abra el archivo del instalador de Visual Studio Code.

1. Acepte el contrato de licencia y, a continuación, continúe aceptando la configuración predeterminada hasta que complete la instalación.

1. Abra Visual Studio Code.

1. En la **barra de actividad**, seleccione **extensiones**.

    La barra de actividad es el menú vertical del lado izquierdo de la interfaz de usuario de Visual Studio Code.

1. En el cuadro **Buscar extensiones en el marketplace**, escriba **C#**.

    Al escribir "C#" se filtra la lista de extensiones para mostrar solo las que tienen algo que ver con la codificación de C#.

1. En la lista filtrada de extensiones disponibles, seleccione la extensión etiquetada "**Kit de desarrollo de C#**: extensión oficial de C# de Microsoft" publicada por Microsoft.

1. Para instalar esta extensión, seleccione **Instalar**.

1. Espere a que termine la instalación.

    El kit de desarrollo de C# tarda aproximadamente 1 minuto en instalarse.

1. En la vista **EXTENSIONES**, reemplace **C#** por **docker**.

1. En la lista filtrada de extensiones disponibles, seleccione la extensión con la etiqueta **Docker** publicada por Microsoft.

1. Para instalar esta extensión, seleccione **Instalar**.

1. En la vista **EXTENSIONES**, reemplace **docker** por **azure app service**.

1. En la lista filtrada de extensiones disponibles, seleccione la extensión con la etiqueta **Azure App Service** publicada por Microsoft.

1. Para instalar esta extensión, seleccione **Instalar**.

1. Cierre Visual Studio Code.

### Ejercicio 3: Crear y configurar recursos de implementación de aplicaciones

En este ejercicio, configurará un proyecto de Azure DevOps y Azure Pipeline, creará e insertará una imagen de Docker en Container Registry e implementará un agente de Windows autohospedado.

Las tareas siguientes tardarán aproximadamente 40 minutos en completarse:

- Configure un proyecto de Azure DevOps e inicialice el repositorio de código.
- Cree una aplicación .NET y sincronice con el repositorio de Azure DevOps.
- Cree una imagen de Docker e inserte la imagen en Azure Container Registry.
- Cree una canalización de Azure denominada Pipeline1.
- Implementar un agente de Windows autohospedado.

#### Tarea 1: Configurar el proyecto de Azure DevOps e inicializar el repositorio de código

Complete los pasos siguientes para configurar el proyecto de Azure DevOps.

1. Abra una ventana del explorador y vaya a Azure Portal: `https://portal.azure.com/`

1. En la barra de búsqueda superior, en el cuadro de búsqueda, escriba **devops**.

1. En los resultados de búsqueda, seleccione **Organizaciones de Azure DevOps**.

1. Seleccione **Mis organizaciones de Azure DevOps**.

1. En la página Necesitamos más detalles, seleccione **Continuar**.

1. En la página Introducción a Azure DevOps, seleccione **Crear nueva organización** y, a continuación, seleccione **Continuar**.

1. En la página Ya casi estamos, escriba los caracteres mostrados y, a continuación, seleccione **Continuar**.

1. En la página Organización de Azure DevOps, seleccione **Configuración de la organización**.

1. En el menú de la izquierda, en **Seguridad**, seleccione **Directivas**.

1. Establezca **Permitir proyectos públicos** en **Activado** y, a continuación, seleccione **Guardar**.

1. Vuelva a la página de la organización de DevOps.

1. En Crear un proyecto para empezar, escriba la siguiente información:

    - Nombre del proyecto: **AZ2003Project**
    - Descripción: **Proyecto de código AZ2003**
    - Visibilidad: **Pública**.

1. Seleccione **Crear**.

1. Selecciona **AZ2003Project**.

1. En el menú de la izquierda de la página de AZ2003Project, selecciona **Repositorios**.

1. En Todos los repositorios, selecciona **AZ2003Project** y, a continuación, selecciona **Examinar**.

1. Seleccione **Clonar en VS Code**.

1. En el cuadro de diálogo Este sitio está intentando abrir Visual Studio Code, seleccione **Abrir**.

1. En el cuadro de diálogo Permitir que una extensión abra este URI, seleccione **Abrir**.

1. En la ventana Elegir una carpeta para clonar, seleccione **Escritorio**, **Nueva carpeta**, escriba **AZ2003** y presione Entrar.

1. Seleccione **Seleccionar como destino del repositorio**.

1. En el cuadro de diálogo ¿Desea abrir el repositorio clonado?, seleccione **Abrir** y, a continuación, seleccione **Sí, confío en los autores**.

#### Tarea 2: Crear una aplicación .NET y sincronizar con el repositorio de Azure DevOps

Complete los pasos siguientes para crear una aplicación .NET y sincronizar con el repositorio de Azure DevOps.

1. En el menú Terminal de Visual Studio Code, seleccione **Nuevo terminal**.

1. En el símbolo del sistema del terminal, para comprobar que el SDK de .NET se ha instalado correctamente, escriba el siguiente comando:

    ```dotnetcli
    dotnet --version
    ```

    Si recibe un error que indica que no se reconoce el término "dotnet", complete lo siguiente:

    - En el menú Inicio de Windows, abra la **Configuración** de Windows.
    - En Configuración, abra la pestaña **Aplicaciones** y, a continuación, seleccione **Aplicaciones instaladas**.
    - Busque el **SDK de Microsoft .NET 8.0.100 (x64)** en la lista de aplicaciones instaladas.
    - A la derecha del SDK de Microsoft .NET 8.0.100 (x64), seleccione los puntos suspensivos (...) y, a continuación, seleccione **Modificar**.
    - Para permitir que la aplicación realice cambios, seleccione **Sí**.
    - En la ventana de SDK de Microsoft .NET 8.0.100, seleccione **Reparar**.
    - Espere a que la operación de reparación se complete correctamente y seleccione **Cerrar**.
    - Cierre la ventana Configuración.
    - Vuelva a la ventana de Visual Studio Code y, a continuación, cierre Visual Studio Code.
    - Vuelva a abrir Visual Studio Code.
    - En el terminal de Visual Studio Code, escriba `dotnet --version` en el símbolo del sistema
    - Debería ver que se muestra un número de versión. Por ejemplo: **8.0.100**.

1. En el símbolo del sistema del terminal, para configurar la configuración de correo electrónico de Git, use el siguiente comando:

    Escriba **git config --global user.email** seguido de la información de correo electrónico de la cuenta proporcionada en el entorno de laboratorio.

    Por ejemplo: git config --global user.email LabUser-12345678@labhoster.onmicrosoft.com

1. En el símbolo del sistema del terminal, para configurar el nombre de usuario de Git, use el siguiente comando:

    Escriba **git config --global user.name** seguido de la información de nombre de usuario de la cuenta proporcionada en el entorno de laboratorio.

    Por ejemplo: git config --global user.name LabUser-12345678

1. En el menú Vista, seleccione **Paleta de comandos**.

1. En el símbolo del sistema, seleccione **.NET: Nuevo proyecto** y, a continuación, seleccione **ASP.NET Core Empty**.

1. Espere a que los recursos se carguen y escriba la siguiente información:

    - En el cuadro de texto Nombre del nuevo proyecto, escriba **AZ2003App**.
    - Acepte el directorio Predeterminado.

1. Abra el símbolo del sistema del terminal y, a continuación, ejecute el siguiente comando de la CLI de dotnet:

    ```dotnetcli
    dotnet build
    ```

1. En la carpeta del proyecto raíz, cree un archivo .gitignore que contenga la siguiente información:

    ```gitignore
    [Bb]in/
    [Oo]bj/
    ```

1. En el menú Archivo, seleccione **Guardar todo**.

1. Abra la vista Control de código fuente.

1. En el cuadro de texto del mensaje de confirmación, escriba la **confirmación inicial**.

1. Seleccione **Confirmar** y, a continuación, seleccione **Sí** para almacenar provisionalmente y confirmar los cambios.

1. Seleccione **Sincronizar cambios** y, a continuación, seleccione **Aceptar** para sincronizar los archivos con el repositorio de DevOps.

1. En el cuadro de diálogo Administrador de credenciales de Git, escriba las credenciales del entorno de laboratorio (nombre de usuario y contraseña).

#### Tarea 3: Crear una imagen de Docker e insertar la imagen en Azure Container Registry

Complete los pasos siguientes para crear una imagen de Docker e insertar la imagen en Azure Container Registry.

1. Asegúrese de que tiene abierto el proyecto de código AZ2003 en Visual Studio Code.

1. Para crear un Dockerfile, ejecute el siguiente comando desde la paleta de comandos: **Docker: Agregue archivos de Docker al área de trabajo**.

1. Cuando se le solicite, especifique la siguiente información:

    - Plataforma de aplicaciones: **.NET ASP.NET Core**.
    - Sistema operativo: **Linux**.
    - Puertos: **5000**.
    - Incluya archivos de Docker Compose: **No**

1. Para crear una imagen de Docker, ejecute el siguiente comando en la paleta de comandos: **Imágenes de Docker: Compile la imagen**.

1. Espere a que se complete el proceso de compilación de imágenes y cierre el terminal.

1. En el menú del lado izquierdo, para abrir la vista Docker, seleccione Docker.

1. Antes de conectarse al registro, asegúrese de que se ha autenticado correctamente con Azure en Visual Studio Code:

    - En la barra de actividades, seleccione **Azure** (el icono de Azure).
    - En la vista de Azure, expanda **CUENTAS e INQUILINOS**.
    - Compruebe que la cuenta de Azure aparece y está seleccionada (activada). Si no es así, seleccione su cuenta para autenticarse.
    - Si necesita iniciar sesión, seleccione **Iniciar sesión en Azure** y complete el proceso de autenticación.

1. En la vista DOCKER, en Registros, seleccione **Conectar registro** y, a continuación, seleccione **Azure Container Registry**.

1. En la vista DOCKER, expanda **Azure** y, a continuación, seleccione **Permitir**.

1. En la ventana del explorador, seleccione la cuenta de Azure que usa para este laboratorio.

1. Vuelva a Visual Studio Code.

1. En la vista DOCKER, expanda la suscripción de Azure y compruebe que la instancia de Azure Container Registry que ha creado aparece en la lista.

1. Para insertar la imagen de Docker en Azure Container Registry, ejecute el siguiente comando en la paleta de comandos: **Imágenes de Docker: Insertar**.

1. Cuando se ejecute el comando, complete los pasos siguientes:

    - Seleccionar grupo de imágenes: seleccione **az2003project**
    - Seleccionar imagen (etiqueta): seleccione la **versión más reciente**
    - Seleccionar el proveedor del registro: seleccione **Azure**
    - Seleccione su suscripción.
    - Seleccionar una instancia de Azure Container Registry en la que insertar: seleccione el registro de contenedor que ha creado. Por ejemplo: acraz2003cah12oct.
    - Para implementar la imagen, presione Entrar.

1. Espere a que se complete el proceso de inserción de imágenes y cierre el terminal.

1. Abra la vista Control de código fuente, escriba un mensaje de confirmación y, a continuación, **Confirme** y **Sincronice los cambios**.

#### Tarea 4: Crear una canalización de Azure denominada Pipeline1

Complete los pasos siguientes para crear una canalización de Azure denominada Pipeline1.

1. Abra el proyecto de Azure DevOps.

1. En el menú de la izquierda, seleccione **Canalizaciones**.

1. Seleccione **Crear canalización**.

1. Seleccione **Git de Azure Repos**.

1. En la página Seleccionar un repositorio, seleccione **AZ2003Project**.

1. Seleccione **Canalización inicial**.

1. En Guardar y ejecutar, seleccione **Guardar** y, a continuación, seleccione **Guardar**.

1. Para cambiar el nombre de la canalización a "Pipeline1", complete los pasos siguientes:

    1. En el menú de la izquierda, seleccione **Canalizaciones**.

    1. A la derecha de la canalización de AZ2003Project, seleccione **Más opciones** y, a continuación, seleccione **Cambiar nombre o mover**.

    1. En el cuadro de diálogo Cambiar nombre o mover canalización, en Nombre, escriba **Pipeline1** y, a continuación, seleccione **Guardar**.

#### Tarea 5: Implementar un agente de Windows autohospedado

Para que una canalización de Azure compile e implemente Windows, Azure y otras soluciones de Visual Studio, necesita al menos un agente de Windows en el entorno de host.

Complete los pasos siguientes para implementar un agente de Windows autohospedado.

1. Vaya a la página principal de la organización de DevOps.

1. En la esquina superior derecha, seleccione **Configuración del usuario**.

1. En el cuadro de diálogo Configuración del usuario, seleccione **Tokens de acceso personal**.

1. Para crear un token de acceso personal, seleccione **+ Nuevo token**.

1. En Nombre, escriba **AZ2003**.

1. En la parte inferior de la ventana Crear un nuevo token de acceso personal, para ver la lista completa de ámbitos, seleccione **Mostrar todos los ámbitos**.

1. Para el ámbito, seleccione **Grupos de agentes (lectura, administración)** y **Grupo de implementación (lectura, administración)**.

    Asegúrese de que todas las demás casillas estén desactivadas.

1. Seleccione **Crear**.

1. En la página Correcto, para copiar el token, seleccione **Copiar en el Portapapeles** y, a continuación, seleccione **Cerrar**.

1. Abra el Bloc de notas y guarde una copia del token en el Bloc de notas.

    Usará este token al configurar el agente.

1. Seleccione la organización de DevOps y, a continuación, seleccione **Configuración de la organización**.

1. En el menú del lado izquierdo en Canalizaciones, seleccione **Grupos de agentes**.

1. Si se abre el cuadro de diálogo **Obtener el agente**, vaya al paso siguiente; de lo contrario, complete los pasos siguientes:

    1. Para seleccionar el grupo predeterminado, seleccione **Predeterminado**.

        Si el grupo **predeterminado** no existe, seleccione **Agregar grupo** y escriba la siguiente información:

        1. En Tipo de grupo, seleccione **Autohospedado**.

        1. En Nombre, escriba **Predeterminado**.

        1. Seleccione **Crear**.

        1. Para abrir el grupo que acaba de crear, seleccione **Predeterminado**.

    1. En Predeterminado, seleccione la pestaña **Agentes** y, a continuación, seleccione **Nuevo agente**.

1. En el cuadro de diálogo Obtener el agente, complete los pasos siguientes:

    1. Seleccione la pestaña **Windows**.

    1. En el panel izquierdo, seleccione **x64**.

    1. En el panel derecho, seleccione **Descargar**.

1. Espere a que se complete la descarga.

1. Cierre el cuadro de diálogo Obtener el agente.

    La siguiente serie de pasos de instrucción le guiarán por el proceso "Crear el agente".

1. Use el Explorador de archivos de Windows para crear la siguiente ubicación de carpeta para el agente:

    ```dos
    C:\agents
    ```

1. Use el Explorador de archivos de Windows para desempaquetar el archivo ZIP del agente descargado en el directorio de agentes que acaba de crear.

1. Espere a que se complete el proceso de extracción de archivos y, a continuación, cierre el Explorador de archivos.

1. Abra Windows PowerShell como administrador, vaya al directorio de agentes y escriba el siguiente comando de PowerShell:

    ```powershell
    .\config
    ```

1. Responda a las indicaciones de configuración de la siguiente manera:

    - Escriba la dirección URL del servidor >: escriba la dirección URL de la organización de DevOps. Por ejemplo: `https://dev.azure.com/<your organization>`
    - Escriba el tipo de autenticación (presione Entrar para PAT) >: presione Entrar.
    - Escriba el token de acceso personal >: Pegue el token de acceso personal que copió en el Bloc de notas.
    - Escriba el grupo de agentes (presione Entrar para el valor predeterminado) >: presione Entrar.
    - Escriba el nombre del agente (presione Entrar para YOUR-PC-NAME) > escriba **az2003-agent**
    - Abra la carpeta de trabajo (presione Entrar para _work) >: presione Entrar.
    - Introduce ¿ejecutar agente como servicio? (Y/N) (presione Entrar para N) >: escriba **Y**
    - Escriba habilitar SERVICE_SID_TYPE_UNRESTRICTED para el servicio del agente (Y/N) (presione Entrar para N) >: escriba **Y**
    - Ingrese la cuenta de usuario que se usará para el servicio (presione Entrar para NT AUTHORITY\NETWORK SERVICE) >: presione Entrar.
    - Introduce si se va a impedir que el servicio se inicie inmediatamente después de que finalice la configuración (Y/N) (presione Entrar para N) >: presione Entrar.

    Se muestra un mensaje que le informa de que el agente se inició correctamente.

    Para obtener ayuda adicional, consulte la siguiente documentación: `https://learn.microsoft.com/azure/devops/pipelines/agents/windows-agent`

1. Cierra Windows PowerShell.

### Ejercicio 4: Configurar Azure Container Registry para una conexión segura con Azure Container Apps

Se le ha pedido que configure los recursos de Azure para cumplir los siguientes requisitos:

- El grupo de recursos debe incluir una identidad administrada asignada por el usuario.
- El registro de contenedor debe poder usar la identidad administrada para extraer artefactos.
- El acceso a la identidad administrada debe limitarse mediante el principio de privilegios mínimos.
- El registro de contenedor debe ser accesible desde un punto de conexión privado en VNET1/PESubnet.

En este ejercicio, configurará una instancia de registro de contenedor para lograr una conexión segura con una aplicación de contenedor.

Las tareas siguientes tardarán unos 10 minutos en completarse:

- Configure una identidad administrada asignada por el usuario.
- Configure el registro de contenedor con permisos AcrPull para la identidad administrada.
- Configure el registro de contenedor con una conexión de punto de conexión privado.

#### Tarea 1: Configurar una identidad administrada asignada por el usuario

Complete los pasos siguientes para configurar una identidad administrada asignada por el usuario.

1. Abra Azure Portal.

1. En la barra de búsqueda superior de Azure Portal, escriba **identidad administrada**.

1. En la lista filtrada de recursos, seleccione **Identidad administrada asignada por el usuario**.

1. En la página Crear identidad administrada asignada por el usuario, especifique la siguiente información:

    - Suscripción: Especifique la suscripción de Azure que usa para este proyecto guiado.
    - Grupo de recursos: **RG1**
    - Región: Escriba la región que coincide con el valor de región del grupo de recursos.
    - Nombre: **uai-az2003**

1. Seleccione **Revisar + crear**.

1. Espere mientras se valida la configuración y, a continuación, seleccione **Crear**.

1. Cierre la página de identidad administrada.

#### Tarea 2: Configurar el registro de contenedor con permisos AcrPull para la identidad administrada

Complete los pasos siguientes para configurar Container Registry con permisos AcrPull para la identidad administrada.

1. En Azure Portal, abra el recurso de Container Registry.

1. En el menú de la izquierda, seleccione **Control de acceso (IAM)**.

1. En la página Control de acceso (IAM), seleccione **Agregar asignaciones de roles**.

1. Busque el rol AcrPull y, a continuación, seleccione **AcrPull**.

1. Seleccione **Siguiente**.

1. En la pestaña Miembros, a la derecha de Asignar acceso a, seleccione **Identidad administrada**.

1. Seleccione **+ Seleccionar integrantes**.

1. En la página Seleccionar identidades administradas, en Identidad administrada, seleccione **Identidad administrada asignada por el usuario** y, a continuación, seleccione la identidad administrada asignada por el usuario creada para este proyecto.

    Por ejemplo: uai-az2003.

1. En la página Seleccionar identidades administradas, seleccione **Seleccionar**.

1. En la pestaña Miembros de la página Agregar asignación de roles, seleccione **Revisar y asignar**.

1. En la pestaña Revisar y asignar, seleccione **Revisar y asignar**.

1. Espere a que se agregue la asignación de roles.

    Aparecerá una notificación, pero si la pierde, puede comprobar la pestaña Asignaciones de roles para comprobar que se ha asignado el rol AcrPull a uai-az2003.

#### Tarea 3: Configurar el registro de contenedor con una conexión de punto de conexión privado

Complete los pasos siguientes para configurar el registro de contenedor con una conexión de punto de conexión privado.

1. Asegúrese de que el recurso de Container Registry esté abierto en el portal.

1. En Configuración, seleccione **Redes**.

1. En la pestaña Acceso privado, seleccione **+ Crear una conexión de punto de conexión privado**.

1. En la pestaña Aspectos básicos, en Detalles del proyecto, especifique la siguiente información:

    - Suscripción: Especifique la suscripción de Azure que usa para este proyecto guiado.
    - Grupo de recursos: **RG1**
    - Nombre: **pe-acr-az2003**
    - Región: Asegúrese de que la región especificada coincida con el valor de región del grupo de recursos.

1. Seleccione **Siguiente: Resource** (Siguiente: Recurso).

1. En la pestaña Recurso, asegúrese de que se muestra la siguiente información:

    - Suscripción: Asegúrese de que está seleccionada la suscripción de Azure que usa para este proyecto guiado.
    - Tipo de recurso: Asegúrese de que **Microsoft.ContainerRegistry/registries** está seleccionado.
    - Recurso: Asegúrese de que el nombre del registro está seleccionado.
    - Recurso secundario de destino: Asegúrese de que **registro** está seleccionado.

1. Seleccione **Siguiente: Máquinas virtuales**.

1. En la pestaña Red virtual, en Redes, asegúrese de que se muestra la siguiente información:

    - Red virtual: Asegúrese de que VNET1 esté seleccionado
    - Subred: Asegúrese de que PESubnet esté seleccionado.

1. Seleccione **Next: DNS** (Siguiente: DNS).

1. En la pestaña DNS, en Integración de DNS privado, asegúrese de que se muestra la siguiente información:

    - Integración con una zona DNS privada: Asegúrese de que está seleccionada la opción **Sí**.
    - Zona DNS privada: Tenga en cuenta que se especifica **(nuevo) privatelink.azurecr.io**.

1. Seleccione **Siguiente: Etiquetas**.

1. Seleccione **Siguiente: Review + create** (Revisar y crear).

1. En la pestaña Revisar y crear, cuando aparezca el mensaje Validación superada, seleccione **Crear**.

1. Espere a que la implementación se complete.

1. Cuando vea **La implementación se ha completado**, cierre la página de implementación del punto de conexión privado.

### Ejercicio 5: Crear y configurar una aplicación de contenedor en Azure Container Apps

Se le ha pedido que configure una aplicación de contenedor que cumpla los siguientes requisitos:

- Se implementa en VNET1/ACASubnet.
- Extrae una imagen de un registro de contenedor.
- Se autentica mediante una identidad administrada asignada por el usuario (uai-az2003).
- Usa la aplicación contenedora para conectarse a una instancia de Service Bus mediante el tipo de cliente .NET.
- La aplicación puede ejecutar hasta dos réplicas que se agregan cada vez que hay 1000 solicitudes HTTP simultáneas.

En este ejercicio, implementará una aplicación de contenedor desde una imagen de Azure Container Registry en la plataforma de Azure Container Apps.

Tardará aproximadamente entre 20 y 25 minutos en completar las siguientes tareas:

- Cree una aplicación de contenedor que use una imagen de Azure Container Registry.
- Configure la aplicación de contenedor para autenticarse mediante la identidad asignada por el usuario.
- Configure una conexión entre la aplicación de contenedor y Service Bus.
- Configure las reglas de escalado de HTTP.

#### Tarea 1:  Crear una aplicación de contenedor que use una imagen de Azure Container Registry

Complete los pasos siguientes para crear una aplicación de contenedor que use una imagen de Azure Container Registry.

1. En la barra de búsqueda superior de Azure Portal, escriba **aplicación de contenedor**.

1. En la lista filtrada de recursos, seleccione **Aplicaciones de contenedor**.

1. En la página Aplicaciones de contenedor, seleccione **Crear aplicación de contenedor**.

1. En la pestaña Aspectos básicos, especifique las opciones siguientes:

    - Suscripción: Especifique la suscripción de Azure que usa para este proyecto guiado.
    - Grupo de recursos: **RG1**
    - Nombre de la aplicación de contenedor: **aca-az2003**
    - Región: Asegúrese de que la región especificada coincida con el valor de región de VNET1 (que debe coincidir con la ubicación del grupo de recursos).

        La aplicación contenedora debe estar en la misma región o ubicación que la red virtual para que pueda elegir VNET1 para el entorno administrado. Para este proyecto guiado, mantenga todos los recursos en la región o ubicación especificada para el grupo de recursos.

    - Entorno de Container Apps: Seleccione **Crear nuevo**.

1. En la página Crear entorno de Container Apps, seleccione la pestaña **Redes** y, a continuación, especifique lo siguiente:

    - Usar su propia red virtual: Seleccione **Sí**.
    - Red virtual: Seleccione **VNET1**.
    - Subred de infraestructura: **ACASubnet**.

    > [!NOTE]
    > Si la subred ACASubnet no aparece en la lista, cancele este proceso de creación, abra el recurso de red virtual, ajuste el intervalo de direcciones de ACASubnet a **10.0.2.0/23** y reinicie los pasos para crear el recurso de aplicación de contenedor.

1. En la página Crear entorno de Container Apps, seleccione **Crear**.

1. En la página Crear aplicación de contenedor, seleccione la pestaña Contenedor y, a continuación, especifique lo siguiente:

    - Utilice la imagen de inicio rápido: **Desactive** esta configuración.
    - Nombre: Asegúrese de que se especifica **aca-az2003**.
    - Origen de imagen: Asegúrese de que **Azure Container Registry** está seleccionado.
    - Registro: Obtenga el identificador del registro de contenedor. Por ejemplo: **acraz2003cah12oct.azurecr.io**
    - Imagen: Seleccionar **az2003project**
    - Etiqueta de imagen: Seleccione **más reciente**

1. Seleccione **Revisar + crear**.

1. Una vez superada la comprobación, seleccione **Crear**.

1. Espere a que la implementación se complete.

    > [!NOTE]
    > Esta implementación puede tardar entre 5 y 10 minutos en completarse.

#### Tarea 2: Configurar la aplicación de contenedor para autenticarse mediante la identidad asignada por el usuario

Complete los pasos siguientes para configurar la aplicación contenedora para autenticarse mediante la identidad asignada por el usuario.

1. En Azure Portal, abra la aplicación contenedora que creó.

1. En Seguridad, selecciona **Identidad**.

1. Seleccione la pestaña **Asignado por el usuario**.

1. Seleccione **Agregar una identidad administrada asignada por el usuario**.

1. En la página Agregar identidad administrada asignada por el usuario, seleccione **uai-az2003** y, a continuación, seleccione **Agregar**.

#### Tarea 3: Configurar una conexión entre la aplicación de contenedor y Service Bus

Complete los pasos siguientes para configurar una conexión entre la aplicación de contenedor y Service Bus.

1. En Azure Portal, asegúrese de que tiene abierta la aplicación contenedora.

1. En Configuración, seleccione **Service Connector (versión preliminar)**.

1. Seleccione **Conectar a los servicios**.

1. En la página Crear conexión, especifique lo siguiente:

    - Tipo de servicio: Seleccionar **Service Bus**.
    - Tipo de cliente: Seleccione **.NET**.

1. Seleccione **Siguiente: Autenticación**.

1. En la pestaña Autenticación, seleccione **Identidad administrada asignada por el usuario**.

1. Para cambiar las pestañas, seleccione **Revisar y crear**.

1. Cuando aparezca el mensaje Validación superada, seleccione **Crear**.

1. Espere a que se cree la conexión.

     La conexión de Service Bus se mostrará en la página Conector de servicio (versión preliminar).

#### Tarea 4: Configurar reglas de escalado de HTTP

Complete los pasos siguientes para configurar las reglas de escalado HTTP para la aplicación de contenedor.

1. Asegúrese de que la aplicación contenedora esté abierta en el portal.

1. En el menú de la izquierda en Aplicación, seleccione **Revisiones y réplicas**.

1. Observe el nombre asignado a la revisión activa.

1. En el menú de la izquierda en Aplicación, seleccione **Escalar**.

1. Revisa la actual **Configuración de regla de escalado**, configura las réplicas mínimas y máximas de la siguiente manera:

    - Establezca las réplicas mínimas: 0
    - Establezca las réplicas máximas: 2

1. En Regla de escalado, seleccione **+ Agregar**.

1. En la página Agregar regla de escalado, especifique lo siguiente:

    - Nombre de la regla: Escriba **scalerule-http**.
    - Escriba: Seleccione **Escalado HTTP**.
    - Solicitudes simultáneas: Establezca el valor en **1000**.

1. En la página Agregar regla de escalado, seleccione **Agregar**.

1. En la página Escala, selecciona **Guardar como una nueva revisión**.

1. Asegúrese de que se muestra la nueva regla de escalado.

    Si la regla de escalado no se muestra después de actualizar, comprueba la revisión seleccionada para ver la revisión activa actual y, a continuación, ajusta la revisión seleccionada en la página Escala y réplicas si es necesario.

### Ejercicio 6: Configurar la integración continua mediante Azure Pipelines

Se le ha pedido que configure un entorno de integración continua para Container Apps que cumpla los siguientes requisitos:

- Necesita una tarea de implementación de Azure Container Apps en el entorno de ADO.
- Pipeline1 debe implementar una imagen de contenedor desde el registro de contenedor en la aplicación de contenedor mediante un grupo de agentes autohospedados.
- Debe asegurarse de que la canalización implementa correctamente la imagen al menos una vez.

En este ejercicio, implementará una aplicación contenedora desde una imagen de Azure Container Registry en la plataforma Azure Container Apps.

Las tareas siguientes tardarán unos 10 minutos en completarse:

- Configure Pipeline1 para usar el grupo de agentes autohospedados.
- Configure Pipeline1 con una tarea de implementación de Azure Container Apps.
- Ejecute la tarea de implementación de Pipeline1.

#### Tarea 1: Configurar Pipeline1 para usar el grupo de agentes autohospedados

Complete los pasos siguientes para configurar las canalizaciones para usar el grupo de agentes autohospedados.

1. Asegúrese de que tiene abierta su organización de Azure DevOps en su propia pestaña del explorador.

    Si es necesario, abra una nueva pestaña del explorador, vaya a `https://dev.azure.com` y, a continuación, abra la organización de Azure DevOps.

1. En la página de Azure DevOps, para abrir el proyecto de DevOps, seleccione **AZ2003Project**.

1. En el menú de la izquierda, seleccione **Canalizaciones**.

1. Seleccione **Pipeline1** y, después, **Editar**.

1. Para usar el grupo de agentes autohospedados, actualice el archivo azure-pipelines.yml como se muestra en el ejemplo siguiente:

    ```yml
    trigger:
    - main

    pool:
      name: default

    steps:

    ```

1. Seleccione **Guardar**.

1. Escriba un mensaje de confirmación y, a continuación, seleccione **Guardar**.

#### Tarea 2: Configurar Pipeline1 con una tarea de implementación de Azure Container Apps

Complete los pasos siguientes para configurar Pipeline1 con una tarea de implementación de Azure Container Apps.

1. Asegúrese de que Pipeline1 está abierto para su edición.

1. En el lado derecho, en Tareas, en el campo Buscar tareas, escriba **contenedor de azure**

1. En la lista filtrada de tareas, seleccione **Implementación de Azure Container Apps**

1. En Conexión de Azure Resource Manager, seleccione la suscripción que usa y, a continuación, seleccione **Autorizar**.

1. En la pestaña Azure Portal, abra el recurso Aplicación contenedora y, a continuación, abra la página Contenedores.

1. Copie la siguiente información en el Bloc de notas.

    - Nombre
    - Registro
    - Imagen
    - Etiqueta de imagen

1. Use la información que copió de la página Contenedores para configurar los siguientes campos de información de tareas:

    - Imagen de Docker que se va a implementar: Registro/Imagen: etiqueta de imagen (reemplace por la información del Bloc de notas)
    - Nombre de la instancia de Azure Container Apps: Nombre (reemplace por la información del Bloc de notas)

    Por ejemplo:

    - Imagen de Docker que se va a implementar: acraz2003cah12oct.azurecr.io/az2003project:latest
    - Nombre de la instancia de Azure Container App: aca-az2003

1. En el campo Nombre del grupo de recursos de Azure, escriba **RG1**

    > [!NOTE]
    > Si necesita comprobar el nombre del grupo de recursos, puede encontrarlo en la página Información general del recurso de la aplicación contenedora.

1. En la página Implementación de Azure Container Apps, seleccione **Agregar**.

    El archivo Yaml de la canalización debe incluir ahora las tareas de AzureContainerApps de la siguiente manera:

    ```yml
    trigger:
    - main
    pool:
      name: default
    steps:
    - task: AzureContainerApps@1
      inputs:
        azureSubscription: '<Subscription>(<Subscription ID>)'
        imageToDeploy: '<Registry>/<Image>:<Image tag>' from Container App resource
        containerAppName: '<Name>' from Container App resource 
        resourceGroup: '<resource group name>'
    
    ```

    Este es un ejemplo que muestra un fragmento de código de configuración de YAML:

    ```yml
    trigger:
    - main
    pool:
      name: default
    steps:
    - task: AzureContainerApps@1
      inputs:
        azureSubscription: 'Visual Studio Enterprise(1111aaaa-22bb-33cc-44dd-555555eeeeee)'
        imageToDeploy: 'acraz2003cah12oct.azurecr.io/aspnetcorecontainer:latest'
        containerAppName: 'aca-az2003'
        resourceGroup: 'RG1'
    ```

1. En la página Pipeline1, seleccione **Guardar**, escriba un mensaje de confirmación y, a continuación, seleccione **Guardar** de nuevo para confirmar.

#### Tarea 3: Ejecutar la tarea de implementación de Pipeline1

Complete los pasos siguientes para ejecutar la tarea de implementación de Pipeline1.

1. Asegúrese de que tiene Pipeline1 abierto en Azure DevOps.

1. Para ejecutar la tarea AzureContainerApps, seleccione **Ejecutar**.

1. En la página Ejecutar canalización, seleccione **Ejecutar**.

    Se abre una página de canalización para mostrar el trabajo asociado. En la sección de trabajo se muestra el estado del trabajo, que avanza de En cola a En espera.

1. Compruebe si se muestra "Permiso necesario" en Trabajo.

    Si el trabajo requiere permiso para continuar, seleccione **Ver** y, a continuación, seleccione **Permitir** para proporcionar los permisos necesarios.

1. Supervise el estado de la operación de ejecución y compruebe que la ejecución es correcta.

    El trabajo en cola puede tardar unos minutos en empezar a ejecutarse. Después de un minuto aproximadamente, el estado del trabajo debe cambiar de "En ejecución" a "Correcto".

### Ejercicio 7: Administrar revisiones en Azure Container Apps

Se le ha pedido que configure la división de tráfico para que las aplicaciones de contenedor cumplan los requisitos siguientes:

- Debe crear una nueva revisión de la aplicación de contenedor que use un sufijo de v2.
- Debes asegurarte de que el 25 % de las solicitudes a la aplicación se dirigen a la revisión v2.
- Debe etiquetar las revisiones "current" y "updated" y asegurarse de que las solicitudes a la revisión "---updated" se dirigen a la revisión con la etiqueta v2.

En este ejercicio, implementará una nueva revisión de la aplicación contenedora y configurará la división de tráfico entre dos revisiones etiquetadas.

Tardará aproximadamente entre 5 y 10 minutos en completar las siguientes tareas:

- Establezca la administración de revisiones en varias.
- Cree una nueva revisión con un sufijo v2.
- Configure las etiquetas en las revisiones.
- Configure un porcentaje de tráfico en las revisiones.

#### Tarea 1: Establecer la administración de revisiones en Varios

Complete los pasos siguientes para establecer la administración de revisiones en Varios.

1. En Azure Portal, abra el recurso de la aplicación de contenedor.

1. En el menú de la izquierda en Aplicación, seleccione **Revisiones y réplicas**.

1. En la parte superior de la página Revisiones, seleccione **Elegir modo de revisión**.

1. Para cambiar del modo de una sola a varias revisiones, seleccione **Confirmar**.

1. En la página Revisiones, espere a que se actualice la configuración **modo de revisión**.

    El modo de revisión se establecerá en **Varios** después de que se complete la actualización.

#### Tarea 2: Crear una nueva revisión con un sufijo v2

Complete los pasos siguientes para crear una nueva revisión con un sufijo v2.

1. En Azure Portal, asegúrese de que tiene abierta la página Revisiones del recurso de aplicación contenedor.

1. En la parte superior de la página, seleccione **+ Crear nueva revisión**.

1. En la página Crear e implementar una nueva revisión, complete los pasos siguientes:

    - Nombre o sufijo: Escriba **v2**
    - En Imagen de contenedor, seleccione la imagen de contenedor. Por ejemplo, aca-az2003.

1. Seleccione **Crear**.

1. Espere a que la implementación se complete.

#### Tarea 3: Configurar etiquetas en las revisiones

La entrada debe estar habilitada para poder configurar las etiquetas de revisión o la división de tráfico.

Complete los pasos siguientes para configurar etiquetas en las revisiones.

1. En el menú de la izquierda, en Redes, selecciona **Entrada**.

1. Si la entrada no está habilitada, seleccione **Habilitado**.

1. En la página Entrada, especifique la siguiente información:

    - Tráfico de entrada: seleccione **Aceptar tráfico desde cualquier lugar**.

    - Tipo de entrada: seleccione **HTTP**.

    - Modo de certificado de cliente: asegúrese de que **Omitir** está seleccionado.

    - Transporte: asegúrese de que **Auto** está seleccionado.

    - Conexiones no seguras: asegúrese de que Permitido **no** está activado.

    - Puerto de destino: escriba **5000**

    - Modo de restricciones de seguridad de IP: asegúrese de que está seleccionado **Permitir todo el tráfico**.

1. En la parte inferior de la página Entrada, seleccione **Guardar** y espere a que se complete la actualización.

1. En el menú de la izquierda, en Revisiones, seleccione **Revisiones y réplicas**.

1. Para la revisión v2, en Etiqueta, escriba **actualizado**.

1. Para la otra revisión, escriba **actual**.

1. En la parte superior de la página, seleccione **Guardar**.

1. Espere a que se actualice la configuración de entrada.

#### Tarea 4: Configurar un porcentaje de tráfico en las revisiones

Complete los pasos siguientes para configurar un porcentaje de tráfico en las revisiones.

1. Asegúrese de que tiene abierta la página Revisiones.

1. Para la revisión v2, en Tráfico, escriba **25** como porcentaje.

1. Para la otra revisión, en Tráfico, escriba **75** como porcentaje.

1. En la parte superior de la página, seleccione **Guardar**.
