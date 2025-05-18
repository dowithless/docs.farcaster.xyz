# Cómo mostrar tu video en Warpcast

1. Asegúrate de servir tu video como un archivo `.m3u8` transmitible. Esto garantiza que los clientes solo descarguen lo necesario al visualizarlo, proporcionando una experiencia de alto rendimiento.

2. Verifica que el manifiesto `.m3u8` incluya la(s) resolución(es) de tu video. Warpcast utiliza esto para determinar la relación de aspecto correcta al renderizar. Un archivo de manifiesto con datos de resolución se ve así:

```
#EXTM3U
#EXT-X-VERSION:3

#EXT-X-STREAM-INF:BANDWIDTH=2444200,CODECS="avc1.64001f,mp4a.40.2",RESOLUTION=474x842
480p/video.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=4747600,CODECS="avc1.640020,mp4a.40.2",RESOLUTION=720x1280
720p/video.m3u8
```

3. Asegúrate de que el archivo `.m3u8` esté disponible al momento de publicar el cast. Warpcast verifica una vez y si no hay datos válidos, el cast no mostrará video.

4. Proporciona la misma URL pero cambiando la terminación de `/my-video.m3u8` a `/thumbnail.jpg` con una imagen de vista previa/miniatura para mostrar antes de que el usuario interactúe con el video.

5. Ponte en contacto con el equipo de Warpcast y solicita que habilitemos los videos para tu dominio. Indícanos el formato de tus URLs de video y configuraremos nuestros scrapers para mostrarlos correctamente en el feed. Por favor, completa todos los pasos anteriores antes de solicitar la inclusión en la lista blanca. [Contacta a @gt en Warpcast](https://warpcast.com/~/inbox/create/302?text=Completed%20video%20setup).
