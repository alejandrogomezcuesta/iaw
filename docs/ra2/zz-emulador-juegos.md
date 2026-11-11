# Emulador de juegos

Un emulador de juegos antiguos

```bash
 docker run -d \
   --name retroassembly \
   -p 8000:8000 \
   -v ./data:/app/data \
   --restart unless-stopped \
   arianrhodsandlot/retroassembly
```