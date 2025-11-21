*Tenga en cuenta que esta aplicación / muestra se proporciona tal cual con fines de demostración sin ninguna garantía de soporte*

Este es un fork de [https://github.com/darryncampbell/darryncampbell-cordova-plugin-intent](https://github.com/darryncampbell/darryncampbell-cordova-plugin-intent).

Actualmente se encuentra en proceso de migración a Capacitor. De momento, la única modificación realizada es la eliminación de la dependencia de `REQUEST_INSTALL_PACKAGES`, que era innecesaria y bloqueaba los despliegues en Google Play.

!npm version
!npm downloads
!npm downloads
!npm licence

Nota: esta es la implementación subyacente actual para https://www.npmjs.com/package/@ionic-native/web-intent y https://ionicframework.com/docs/native/web-intent/

# Soporte para Android X
- Para soporte de Android X, utilice la versión >= 2.x.x 
- Para la Biblioteca de Soporte de Android, utilice la versión 1.3.x

# Interacción con el Plugin de Cámara
Si está instalando este plugin junto con cordova-plugin-camera, **DEBE instalar cordova-plugin-camera primero.**

# Resumen
Este plugin de Cordova proporciona una capa de shim de propósito general para el mecanismo de intent de Android, exponiendo varias formas de manejar el envío y la recepción de intents.

## Créditos
Este proyecto utiliza código publicado bajo los siguientes proyectos MIT:
- https://github.com/napolitano/cordova-plugin-intent (marcado como ya no mantenido)
- https://github.com/Initsogar/cordova-webintent.git (ya no está disponible en github pero el proyecto está bifurcado aquí: https://github.com/darryncampbell/cordova-webintent)
Este proyecto también se publica bajo MIT. El crédito se da en el código cuando es apropiado.

## IntentShim
Este plugin define un objeto `window.plugins.intentShim` que proporciona una API para interactuar con el mecanismo de intent de Android en cualquier dispositivo Android.

## Pruebas / Ejemplo
Una aplicación de ejemplo está disponible en https://github.com/darryncampbell/plugin-intent-api-exerciser para demostrar la API y se puede utilizar para probar la funcionalidad.

## Instalación

### Cordova Versión < 7
    cordova plugin add https://github.com/pilar-bernabe-nb/pilarbf-cordova-capacitor-plugin-intent.git

### Cordova Versión >= 7
    cordova plugin add com-pilarbf-cordova-capacitor-plugin-intent

## Uso con PhoneGap

Utilice la última versión de PhoneGap cli al incluir este plugin, consulte el Issue 63 para más contexto. 

## Plataformas Soportadas
- Android

## intentShim.registerBroadcastReceiver

Registra un receptor de difusión para los filtros especificados.

    window.plugins.intentShim.registerBroadcastReceiver(filters, callback);

### Descripción

La función `intentShim.registerBroadcastReceiver` registra un receptor de difusión dinámico para la lista de filtros especificada e invoca el callback especificado cuando se recibe cualquiera de esos filtros.

### Ejemplo

Registrar un receptor de difusión para dos filtros:

    window.plugins.intentShim.registerBroadcastReceiver({
        filterActions: [
            'com.pilarbf.cordovacapacitor.plugin.broadcastIntent.ACTION',
            'com.pilarbf.cordovacapacitor.plugin.broadcastIntent.ACTION_2'
            ]
        },
        function(intent) {
            console.log('Received broadcast intent: ' + JSON.stringify(intent.extras));
        }
    );


## intentShim.unregisterBroadcastReceiver

Anula el registro de cualquier BroadcastReceiver.

    window.plugins.intentShim.unregisterBroadcastReceiver();

### Descripción

La función `intentShim.unregisterBroadcastReceiver` anula el registro de todos los receptores de difusión registrados con `intentShim.registerBroadcastReceiver(filters, callback);`. No se recibirán más difusiones para ningún filtro registrado después de esta llamada.

### Peculiaridades de Android

El desarrollador es responsable de llamar a `unregister` / `register` cuando su aplicación pasa a segundo plano o vuelve a primer plano, si lo desea.

### Ejemplo

Anular el registro del receptor de difusión cuando la aplicación recibe un evento onPause:

    bindEvents: function() {
        document.addEventListener('pause', this.onPause, false);
    },
    onPause: function()
    {
        window.plugins.intentShim.unregisterBroadcastReceiver();
    }

## intentShim.sendBroadcast

Envía un intent de difusión.

    window.plugins.intentShim.sendBroadcast(action, extras, successCallback, failureCallback);

### Descripción

La función `intentShim.sendBroadcast` envía un intent de difusión de Android con una acción especificada.

### Ejemplo

Enviar un intent de difusión a una acción especificada que contiene un número aleatorio en los extras:

    window.plugins.intentShim.startActivity(
        {
            action: "com.pilarbf.cordovacapacitor.plugin.intent.ACTION",
            extras: {
                    'random.number': Math.floor((Math.random() * 1000) + 1)
            }
        },
        function() {},
        function() {alert('Failed to open URL via Android Intent')}
    );


## intentShim.onIntent

Devuelve el contenido del intent utilizado cada vez que se inicia la actividad de la aplicación.

    window.plugins.intentShim.onIntent(callback);

### Descripción

La función `intentShim.onIntent` devuelve el intent que lanzó la Actividad y se asigna al método onNewIntent() de la Actividad de Android, https://developer.android.com/reference/android/app/Activity.html#onNewIntent(android.content.Intent). El callback registrado se invoca cada vez que se inicia la actividad.

### Peculiaridades de Android

Por defecto, la aplicación de Android se creará con el modo de lanzamiento establecido en 'SingleTop'. Si desea cambiar esto a 'SingleTask', puede hacerlo modificando `config.xml` de la siguiente manera:

    <platform name="android">
        ...
        <preference name="AndroidLaunchMode" value="singleTask"/>
    </platform>
Consulte https://www.mobomo.com/2011/06/android-understanding-activity-launchmode/ para obtener más información sobre las diferencias entre los dos.

### Ejemplo

Registra un callback para ser invocado:

    window.plugins.intentShim.onIntent(function (intent) {
        console.log('Received Intent: ' + JSON.stringify(intent.extras));
    });

## intentShim.startActivity

Inicia una nueva actividad utilizando un intent construido a partir de action, url, type, extras o algún subconjunto de esos parámetros.

    window.plugins.intentShim.startActivity(params, successCallback, failureCallback);

### Descripción

La función `intentShim.startActivity` se asigna al método de actividad de Android startActivity, https://developer.android.com/reference/android/app/Activity.html#startActivity(android.content.Intent) para lanzar una nueva actividad.

### Peculiaridades de Android

Algunas acciones comunes se definen como constantes en el plugin, ver más abajo.

### Ejemplos

Lanzar la actividad de mapas:

    window.plugins.intentShim.startActivity(
    {
        action: window.plugins.intentShim.ACTION_VIEW,
        url: 'geo:0,0?q=London'
    },
    function() {},
    function() {alert('Failed to open URL via Android Intent')}
    );

Lanzar el navegador web:

    window.plugins.intentShim.startActivity(
    {
        action: window.plugins.intentShim.ACTION_VIEW,
        url: 'http://www.google.co.uk'
    },
    function() {},
    function() {alert('Failed to open URL via Android Intent')}
    );

## intentShim.getIntent

Recupera el intent que lanzó la actividad.

    window.plugins.intentShim.getIntent(resultCallback, failureCallback);

### Descripción

La función `intentShim.getIntent` se asigna al método de actividad de Android getIntent, https://developer.android.com/reference/android/app/Activity.html#getIntent() para devolver el intent que inició esta actividad.

### Ejemplo

    window.plugins.intentShim.getIntent(
        function(intent)
        {
            console.log('Action' + JSON.stringify(intent.action));
            var intentExtras = intent.extras;
            if (intentExtras == null)
                intentExtras = "No extras in intent";
            console.log('Launch Intent Extras: ' + JSON.stringify(intentExtras));
        },
        function()
        {
            console.log('Error getting launch intent');
        });


## intentShim.startActivityForResult

Inicia una nueva actividad y devuelve el resultado a la aplicación.

    window.plugins.intentShim.startActivityForResult(params, resultCallback, failureCallback);

### Descripción

La función `intentShim.startActivityForResult` se asigna al método de actividad de Android startActivityForResult, https://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent, int) para lanzar una nueva actividad y los datos resultantes se devuelven a través del resultCallback.

### Peculiaridades de Android

Algunas acciones comunes se definen como constantes en el plugin, ver más abajo.

### Ejemplo

Elegir un contacto de Android:

    window.plugins.intentShim.startActivityForResult(
    {
        action: window.plugins.intentShim.ACTION_PICK,
        url: "content://com.android.contacts/contacts",
        requestCode: 1
    },
    function(intent)
    {
        if (intent.extras.requestCode == 1)
        {
            console.log('Picked contact: ' + intent.data);
        }
    },
    function()
    {
        console.log("StartActivityForResult failure");
    });

## intentShim.sendResult

Suponiendo que esta aplicación se inició con `intentShim.startActivityForResult`, envía un resultado de vuelta.

    window.plugins.intentShim.sendResult(args, callback);

### Descripción

La función `intentShim.sendResult` devuelve un Intent `Activity.RESULT_OK` a la actividad que inició esta aplicación, junto with cualquier extra que desee enviar (como objeto `args.extras`), y una función `callback`. Luego llama al método finish() de la Actividad de Android, https://developer.android.com/reference/android/app/Activity.html#finish().

### Peculiaridades de Android

Ambos argumentos `args` y `callback` deben ser proporcionados. Si no necesita la funcionalidad, envíe un objeto vacío y una función vacía.

    window.plugins.intentShim.sendResult({}, function() {});

### Ejemplo

    window.plugins.intentShim.sendResult(
        {
            extras: {
                'Test Intent': 'Successfully sent',
                'Test Intent int': 42,
                'Test Intent bool': true,
                'Test Intent double': parseFloat("142.12")
            }
        },
        function() {
        
        }
    );

## intentShim.packageExists
Devuelve un booleano que indica si un paquete específico está instalado en el dispositivo.

```js
window.plugins.intentShim.packageExists(packageName, callback);
```

### Descripción

La función `intentShim.packageExists` devuelve un booleano que indica si un paquete específico package está instalado en el dispositivo actual.

### Ejemplo
```js
const packageName = 'com.android.contacts';

window.plugins.intentShim.packageExists(packageName, (exists) => {
    if (exists) {
        console.log(`${packageName} exists!`);
    } else {
        console.log(`${packageName} does not exist...`);
    }
});
```

## Constantes Predefinidas

Las siguientes constantes están definidas en el plugin para su uso en JavaScript
- window.plugins.intentShim.ACTION_SEND
- window.plugins.intentShim.ACTION_VIEW
- window.plugins.intentShim.EXTRA_TEXT
- window.plugins.intentShim.EXTRA_SUBJECT
- window.plugins.intentShim.EXTRA_STREAM
- window.plugins.intentShim.EXTRA_EMAIL
- window.plugins.intentShim.ACTION_CALL
- window.plugins.intentShim.ACTION_SENDTO
- window.plugins.intentShim.ACTION_GET_CONTENT
- window.plugins.intentShim.ACTION_PICK

## Versiones Probadas

Probado con la versión 6.5.0 de Cordova y la versión 6.2.1 de Cordova Android.
