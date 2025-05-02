# 🚀 Deploy do Portainer com SSL no Docker e Docker Swarm

Este guia mostra como subir o **Portainer** e o **Portainer Agent** utilizando **SSL com certificados autoassinados**, para garantir conexões seguras, seja em Docker standalone ou em ambiente Docker Swarm.

---

## 🔐 Gerando Certificado SSL Autoassinado

1. Crie um certificado usando `OpenSSL` com base no arquivo `cert.conf`, localizado na pasta `certs`:

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout portainer.key \
  -out portainer.crt \
  -config cert.conf \
  -extensions req_ext
```

⚠️ **Atenção:** os certificados presentes no repositório são **apenas ilustrativos**.

2. Para verificar o conteúdo do SAN (Subject Alternative Name) do certificado:

```
openssl x509 -in portainer.crt -text -noout | grep -A10 "Subject Alternative Name"
```

---

## 📦 Preparando Ambiente

1. Crie o volume do Portainer:

```
docker volume create portainer_data
```

2. Renomeie o arquivo `.crt` para `ca-certificates.crt` e coloque na pasta `certs` do agent.

3. No `docker-compose.yml` do agent, monte o volume assim:

```
- ./certs/ca-certificates.crt:/etc/ssl/certs/ca-certificates.crt:ro
```

---

## 🌐 Subindo o Portainer com SSL

Configure o `nginx` para fazer o redirecionamento do túnel seguro. Após isso, no Portainer, defina a conexão do agente com o túnel HTTPS via WebSocket.

📸 Exemplo de túnel estabilizado:

![Túnel WebSocket Estabilizado com SSL](docs/images/tunel-ok.png)

---

## ✅ Resultado Esperado

- Comunicação segura entre Portainer e Agent.
- Certificado válido localmente com SAN configurado.
- Setup compatível com Docker e Docker Swarm.

---

Feito isso, é só acessar o Portainer com o navegador apontando pro domínio com HTTPS.
