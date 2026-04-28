FROM node:18-alpine AS build
WORKDIR /app

# Build context should be repo root (use: docker build -f frontend/Dockerfile .)
COPY Esprit-PIDEV-4SAE3-2026-Freelancy/package*.json ./
RUN npm ci

COPY Esprit-PIDEV-4SAE3-2026-Freelancy/ ./
RUN npm run build

# Angular output can be dist/<app>, dist/<app>/browser, dist/browser, etc.
# Normalize to /app/dist-out for the Nginx stage.
RUN node -e "const fs=require('fs');const path=require('path');const dist=path.join(process.cwd(),'dist');if(!fs.existsSync(dist)) {console.error('Missing dist/ after build');process.exit(1);} const exists=p=>fs.existsSync(path.join(dist,p)); let pick=null; if(exists('browser')) pick=path.join(dist,'browser'); if(!pick){ const entries=fs.readdirSync(dist,{withFileTypes:true}).filter(d=>d.isDirectory()).map(d=>d.name); for(const e of entries){ if(fs.existsSync(path.join(dist,e,'browser'))) { pick=path.join(dist,e,'browser'); break; } } if(!pick && entries.includes('frontend')) pick=path.join(dist,'frontend'); if(!pick && entries.length===1) pick=path.join(dist,entries[0]); } if(!pick){ console.error('Could not detect Angular dist output (dist/frontend or dist/**/browser)'); process.exit(2);} fs.rmSync(path.join(process.cwd(),'dist-out'),{recursive:true,force:true}); fs.cpSync(pick,path.join(process.cwd(),'dist-out'),{recursive:true}); console.log('Serving folder:',pick);"

FROM nginx:1.27-alpine
COPY --from=build /app/dist-out /usr/share/nginx/html
EXPOSE 80

