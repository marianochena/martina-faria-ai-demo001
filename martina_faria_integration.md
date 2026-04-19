# Martina Faria - AI Assistant Integration Guide (Premium Edition)

¡Hola! Este documento contiene el **Snippet Integral** para integrar el asistente virtual "Martina Faria AI" directamente en el sitio web (WordPress, Hostinger, etc.). Esta solución es **Nativa** (Stitch Custom UI), lo que significa que se mezcla perfectamente con el diseño del sitio sin usar iframes toscos.

## 🛠️ Instrucciones de Instalación

1.  **Sube el Avatar**: Sube el archivo `bot-avatar.png` a tu biblioteca de medios (Wordpress/Servidor).
2.  **Copia el Código**: Copia el bloque de abajo y pégalo justo antes de la etiqueta `</body>` en tu sitio web.
3.  **Actualiza el Avatar**: Busca la línea `background-image: url(...)` en el bloque de estilos y reemplázala con la URL real de tu imagen.

---

### 💻 Snippet de Producción

```html
<!-- ==============================================
     MARTINA FARIA AI CHAT WIDGET (STITCH PREMIUM)
     ============================================== -->

<!-- Fuentes -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
<link href="https://cdn.jsdelivr.net/npm/@n8n/chat/dist/style.css" rel="stylesheet" />

<style>
    /* ----- VARIABLES PREMIUM (Stitch Zen) ----- */
    :root {
      --chat--color-primary: #0f172a; 
      --chat--color-primary-shade-50: #1e293b;
      --chat--color-primary-shade-100: #334155;
      --chat--color-secondary: #f8fafc;
      
      /* Glassmorphism Refinado */
      --chat--window--background: rgba(255, 255, 255, 0.85);
      --chat--window--backdrop-filter: blur(20px) saturate(180%);
      --chat--window--border-radius: 24px;
      --chat--window--box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
      
      /* Mensajes */
      --chat--message--bot--background: #ffffff;
      --chat--message--bot--color: #1e293b;
      --chat--message--user--background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
      --chat--message--user--color: #ffffff;
    }

    /* ----- ESTRUCTURA DEL WIDGET ----- */
    #botWrapper {
        position: fixed;
        bottom: 25px;
        right: 25px;
        z-index: 999999;
        display: flex;
        flex-direction: column;
        align-items: flex-end;
        pointer-events: none;
        font-family: 'Inter', sans-serif;
    }
    #botWrapper > * { pointer-events: auto; }

    /* BOTÓN TOGGLE (Avatar) */
    #stitchChatToggle {
        width: 65px;
        height: 65px;
        border-radius: 50%;
        background-color: #0f172a;
        /* REEMPLAZAR AQUÍ LA URL */
        background-image: url('assets/bot-avatar.png'); 
        background-size: cover;
        background-position: center;
        border: 3px solid #fff;
        box-shadow: 0 10px 25px rgba(15, 23, 42, 0.3);
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }
    #stitchChatToggle:hover {
        transform: scale(1.1) rotate(5deg);
        box-shadow: 0 15px 30px rgba(15, 23, 42, 0.4);
    }

    /* VENTANA DE CHAT */
    #stitchChatWindow {
        width: 400px;
        height: 650px;
        max-height: 80vh;
        max-width: 90vw;
        background: var(--chat--window--background);
        backdrop-filter: var(--chat--window--backdrop-filter);
        -webkit-backdrop-filter: var(--chat--window--backdrop-filter);
        border-radius: var(--chat--window--border-radius);
        box-shadow: var(--chat--window--box-shadow);
        border: 1px solid rgba(255,255,255,0.4);
        margin-bottom: 20px;
        display: flex;
        flex-direction: column;
        overflow: hidden;
        opacity: 0;
        transform: translateY(30px) scale(0.9);
        transform-origin: bottom right;
        transition: all 0.4s cubic-bezier(0.165, 0.84, 0.44, 1);
        pointer-events: none;
    }
    #stitchChatWindow.open {
        opacity: 1;
        transform: translateY(0) scale(1);
        pointer-events: auto;
    }

    /* HEADER PERSONALIZADO */
    #stitchChatHeader {
        background: #0f172a;
        color: white;
        padding: 15px 20px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        height: 65px;
        box-sizing: border-box;
    }
    #stitchChatHeader h3 { margin: 0; font-size: 1.1rem; font-weight: 600; line-height: 1.2; font-family: 'Inter', sans-serif;}
    #stitchChatHeader p { margin: 0; font-size: 0.8rem; color: #94a3b8; font-family: 'Inter', sans-serif;}
    
    .stitch-close-btn {
        background: rgba(255,255,255,0.1);
        border: none; color: white; border-radius: 50%;
        cursor: pointer; width: 28px; height: 28px;
        display: flex; align-items: center; justify-content: center;
    }

    /* CONTENEDOR N8N */
    #n8nContainer {
        height: calc(100% - 65px);
        width: 100%;
        background: transparent;
        overflow: hidden;
    }

    /* FIXES INTERNOS N8N */
    .chat-layout .chat-header { display: none !important; }
    .chat-layout { height: 100% !important; background: transparent !important; }
</style>

<div id="botWrapper">
    <div id="stitchChatWindow">
        <div id="stitchChatHeader">
            <div>
                <h3>Martina Faria AI</h3>
                <p>En línea • Responde al instante</p>
            </div>
            <button class="stitch-close-btn" id="btnCloseChat" aria-label="Cerrar chat">×</button>
        </div>
        <div id="n8nContainer"></div>
    </div>
    <div id="stitchChatToggle" title="Chat con Martina Faria"></div>
</div>

<script type="module">
    import { createChat } from 'https://cdn.jsdelivr.net/npm/@n8n/chat/dist/chat.bundle.es.js';

    const webhookUrl = 'https://n8n.marianochena.com/webhook/a14269ce-7210-4702-b964-1fda468c4abc/chat';
    let initialized = false;

    const chatWin = document.getElementById('stitchChatWindow');
    const toggle = document.getElementById('stitchChatToggle');
    const close = document.getElementById('btnCloseChat');

    toggle.addEventListener('click', () => {
        chatWin.classList.toggle('open');
        if (chatWin.classList.contains('open') && !initialized) {
            createChat({
                webhookUrl: webhookUrl,
                target: '#n8nContainer',
                mode: 'fullscreen',
                showWelcomeScreen: true,
                initialMessages: [
                    '¡Hola! Soy la asistente virtual de Martina Faria. 👋',
                    '¿Cómo puedo ayudarte hoy con tu hipoteca o préstamo?'
                ],
                i18n: { 
                    es: { 
                        chatInput: { placeholder: 'Escribe tu consulta aquí...' },
                        welcomeScreen: { title: 'Bienvenido', subtitle: 'Estamos aquí para ayudarte' }
                    } 
                }
            });
            initialized = true;
        }
    });
    close.addEventListener('click', () => chatWin.classList.remove('open'));
</script>
```
