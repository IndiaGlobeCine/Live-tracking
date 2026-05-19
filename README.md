<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#050508">
    
    <!-- PWA Fullscreen & App Capabilities -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="mobile-web-app-capable" content="yes">
    
    <title>MND Live Logistics | Premium Cloud Tracking</title>

    <!-- Premium Fonts & Icons -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Outfit:wght@300;400;500;700;900&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <!-- Firebase SDKs (Database Only) -->
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>

    <style>
        /* =========================================================================
           GLOBAL RESET & VARIABLES
           ========================================================================= */
        :root {
            --gold: #D4AF37;
            --gold-glow: rgba(212, 175, 55, 0.4);
            --neon-green: #00FA9A;
            --neon-cyan: #00E5FF;
            --bg-dark: #050508;
            --danger: #ff3333;
            
            /* WhatsApp Dark Theme Colors */
            --wa-bg: #0b141a;
            --wa-header: #202c33;
            --wa-sent: #005c4b;
            --wa-received: #202c33;
            --wa-text: #e9edef;
            --wa-time: rgba(255,255,255,0.55);

            /* Modern Input Colors */
            --brand-purple: #7C3AED;
            --input-bg: #1f2937;
            --icon-gray: #9ca3af;
        }

        /* Prevent Pull-To-Refresh globally using overscroll-behavior-y: none */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none; user-select: none; -webkit-user-select: none; }
        body, html { width: 100vw; height: 100dvh; background: var(--bg-dark); color: #fff; overflow: hidden; display: flex; justify-content: center; align-items: flex-start; overscroll-behavior-y: none; }
        input, textarea { user-select: auto; -webkit-user-select: auto; } /* Allow typing */

        /* Cinematic Background */
        .bg-map {
            position: fixed; inset: 0; z-index: -1; opacity: 0.15;
            background-image: 
                radial-gradient(circle at 20% 30%, var(--gold-glow) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(0, 250, 154, 0.15) 0%, transparent 50%);
            animation: pulseBg 6s infinite alternate ease-in-out;
        }
        .grid-overlay {
            position: fixed; inset: 0; z-index: -1; opacity: 0.05;
            background-image: linear-gradient(var(--gold) 1px, transparent 1px), linear-gradient(90deg, var(--gold) 1px, transparent 1px);
            background-size: 40px 40px;
        }
        @keyframes pulseBg { 0% { opacity: 0.1; transform: scale(1); } 100% { opacity: 0.25; transform: scale(1.05); } }

        /* =========================================================================
           NATIVE-STYLE PUSH NOTIFICATIONS, TOASTS & INSTALL BANNER
           ========================================================================= */
        #toast-container { position: fixed; top: 15px; left: 50%; transform: translateX(-50%); z-index: 9999999; display: flex; flex-direction: column; gap: 10px; pointer-events: none; width: 100%; align-items: center; }
        .toast { background: rgba(10,10,15,0.98); border-left: 4px solid var(--gold); color: #fff; padding: 15px 20px; border-radius: 8px; font-weight: 600; font-size: 14px; box-shadow: 0 10px 30px rgba(0,0,0,0.8); display: flex; align-items: center; gap: 10px; max-width: 90%; animation: dropDown 0.4s cubic-bezier(0.2, 0.8, 0.2, 1) forwards, slideUp 0.4s 3.5s forwards; }
        .toast.success { border-color: var(--neon-green); }
        .toast.error { border-color: var(--danger); }
        
        .native-push { background: rgba(20, 20, 25, 0.95); backdrop-filter: blur(20px); border: 1px solid rgba(255, 255, 255, 0.15); border-radius: 18px; padding: 15px; width: 92%; max-width: 400px; display: flex; gap: 15px; box-shadow: 0 20px 40px rgba(0,0,0,0.8), 0 0 20px rgba(212, 175, 55, 0.2); animation: dropDownPush 0.6s cubic-bezier(0.2, 0.8, 0.2, 1) forwards, slideUpPush 0.5s 6s forwards; }
        .native-push-icon { width: 45px; height: 45px; min-width: 45px; border-radius: 12px; background: linear-gradient(135deg, var(--gold), #FFD700); display: flex; align-items: center; justify-content: center; color: #000; font-size: 24px; box-shadow: 0 5px 15px rgba(212,175,55,0.4); }
        .native-push-content { flex-grow: 1; display: flex; flex-direction: column; justify-content: center; }
        .native-push-title { font-family: 'Outfit', sans-serif; font-size: 15px; font-weight: 800; color: #fff; margin: 0 0 3px 0; text-transform: uppercase; letter-spacing: 1px; display:flex; justify-content:space-between; }
        .native-push-title span { font-size: 10px; color: var(--gold); font-weight: 500; text-transform: none; }
        .native-push-body { font-family: 'Outfit', sans-serif; font-size: 13px; color: #ccc; margin: 0; line-height: 1.4; font-weight: 500; }

        @keyframes dropDown { from { opacity: 0; transform: translateY(-30px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes slideUp { from { opacity: 1; transform: translateY(0); } to { opacity: 0; transform: translateY(-30px); } }
        @keyframes dropDownPush { from { opacity: 0; transform: translateY(-50px) scale(0.95); } to { opacity: 1; transform: translateY(0) scale(1); } }
        @keyframes slideUpPush { from { opacity: 1; transform: translateY(0) scale(1); } to { opacity: 0; transform: translateY(-50px) scale(0.95); } }

        /* PWA Install Banner UI */
        .install-banner { position: fixed; top: 0; left: 0; right: 0; background: rgba(20,20,25,0.98); backdrop-filter: blur(15px); padding: 15px 20px; z-index: 99999999; display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid var(--gold); box-shadow: 0 10px 30px rgba(0,0,0,0.8); transform: translateY(-100%); transition: transform 0.4s cubic-bezier(0.2, 0.8, 0.2, 1); }
        .install-banner.show { transform: translateY(0); }

        /* =========================================================================
           VIEW MANAGER (Strict SPA) - NATIVE FULL SCROLL
           ========================================================================= */
        .view-container { display: none; flex-direction: column; align-items: center; width: 100%; height: 100dvh; padding: 25px 15px 60px; animation: fadeInView 0.5s ease forwards; overflow-y: auto; -webkit-overflow-scrolling: touch; overscroll-behavior-y: none; }
        .active-view { display: flex !important; }
        @keyframes fadeInView { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        ::-webkit-scrollbar { width: 4px; } ::-webkit-scrollbar-track { background: transparent; } ::-webkit-scrollbar-thumb { background: rgba(212,175,55,0.4); border-radius: 10px; }

        /* =========================================================================
           UI COMPONENTS (Inputs, Buttons, Cards)
           ========================================================================= */
        .app-title { font-family: 'Cinzel', serif; color: var(--gold); font-size: 28px; font-weight: 900; letter-spacing: 2px; margin-bottom: 5px; text-shadow: 0 0 15px var(--gold-glow); text-align: center; }
        .app-subtitle { color: #aaa; font-size: 13px; margin-bottom: 30px; line-height: 1.5; font-weight: 300; text-align: center; }

        .input-group { width: 100%; position: relative; margin-bottom: 18px; }
        .input-group i { position: absolute; left: 18px; top: 18px; color: var(--gold); font-size: 18px; opacity: 0.8; }
        .mn-input { width: 100%; padding: 16px 16px 16px 50px; background: rgba(0,0,0,0.6); border: 1px solid rgba(212,175,55,0.3); color: #fff; border-radius: 12px; font-size: 16px; outline: none; transition: 0.3s ease; box-shadow: inset 0 2px 10px rgba(0,0,0,0.5); }
        .mn-input:focus { border-color: var(--gold); background: rgba(212,175,55,0.05); box-shadow: 0 0 15px var(--gold-glow), inset 0 2px 10px rgba(0,0,0,0.5); }
        .pin-style { letter-spacing: 12px; font-size: 24px; font-weight: 900; text-align: center; padding-left: 20px; font-family: 'Orbitron', sans-serif; color: var(--neon-cyan); }
        .pin-style + i { display: none; }
        .pin-style::placeholder { color: #555; letter-spacing: 5px; font-size: 16px; font-family: 'Outfit', sans-serif; font-weight: 400; }

        .mn-btn { width: 100%; padding: 16px; border-radius: 12px; font-weight: 800; font-size: 14px; cursor: pointer; transition: all 0.3s ease; display: flex; justify-content: center; align-items: center; gap: 10px; border: none; letter-spacing: 1px; text-transform: uppercase; flex-shrink: 0; }
        .mn-btn:active { transform: scale(0.96); }
        .btn-gold { background: linear-gradient(135deg, var(--gold) 0%, #FFD700 100%); color: #000; box-shadow: 0 5px 25px var(--gold-glow); }
        .btn-green { background: linear-gradient(135deg, #25D366 0%, #128C7E 100%); color: #fff; box-shadow: 0 5px 20px rgba(37, 211, 102, 0.3); }
        .btn-dark { background: rgba(0,0,0,0.5); border: 1px solid var(--gold); color: var(--gold); backdrop-filter: blur(5px); }
        .btn-danger { background: rgba(255,51,51,0.1); border: 1px solid var(--danger); color: var(--danger); margin-top: 15px; }

        .dash-header { width: 100%; max-width: 600px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 15px; flex-shrink: 0; }
        .status-badge { background: rgba(0, 250, 154, 0.1); border: 1px solid var(--neon-green); color: var(--neon-green); padding: 6px 14px; border-radius: 30px; font-size: 11px; font-weight: 800; display: flex; align-items: center; gap: 6px; letter-spacing: 1px; box-shadow: 0 0 15px rgba(0, 250, 154, 0.2); }
        .status-badge.offline { background: rgba(255, 51, 51, 0.1); border-color: var(--danger); color: var(--danger); box-shadow: 0 0 15px rgba(255, 51, 51, 0.2); }
        
        .card { width: 100%; max-width: 600px; background: rgba(10,10,15,0.85); border: 1px solid rgba(255,255,255,0.05); padding: 20px; border-radius: 16px; margin-bottom: 20px; backdrop-filter: blur(15px); box-shadow: 0 10px 30px rgba(0,0,0,0.5); flex-shrink: 0; }
        .card.flex-grow { flex-grow: 1; max-height: none; }
        .card h3 { color: var(--gold); font-family: 'Cinzel'; margin-bottom: 15px; font-size: 16px; border-bottom: 1px dashed rgba(212,175,55,0.3); padding-bottom: 8px; display: flex; align-items: center; gap: 10px; }

        /* =========================================================================
           WHATSAPP-STYLE CHAT ENGINE (PERFECTED WITH SWIPE & LONG PRESS)
           ========================================================================= */
        .chat-card { padding: 0 !important; overflow: hidden; display: flex; flex-direction: column; height: 75vh; max-height: 800px; border: 1px solid rgba(255,255,255,0.1); border-radius: 16px; background: var(--wa-bg) !important; margin-bottom: 20px; width: 100%; max-width: 600px; flex-shrink:0; box-shadow: 0 20px 50px rgba(0,0,0,0.8); }
        
        .chat-header { background: var(--wa-header); padding: 12px 15px; display: flex; justify-content: space-between; align-items: center; z-index: 2; box-shadow: 0 2px 10px rgba(0,0,0,0.5); }
        
        /* Subtle Chat Background Pattern */
        .chat-area { flex-grow: 1; overflow-y: auto; overflow-x: hidden; padding: 20px 15px; display: flex; flex-direction: column; gap: 12px; background-color: var(--wa-bg); background-image: radial-gradient(rgba(255,255,255,0.04) 1px, transparent 1px); background-size: 20px 20px; scroll-behavior: smooth; overscroll-behavior-y: none; }
        
        /* Swipe to Reply Wrapper */
        .bubble-wrapper { display: flex; align-items: center; width: 100%; position: relative; transition: background 0.2s; }
        .reply-swipe-icon { position: absolute; left: 10px; font-size: 20px; color: var(--wa-text); background: rgba(0,0,0,0.5); border-radius: 50%; width: 35px; height: 35px; display: flex; justify-content: center; align-items: center; opacity: 0; transform: scale(0.5); transition: all 0.2s cubic-bezier(0.2, 0.8, 0.2, 1); z-index: 1; }
        
        /* WhatsApp Chat Bubbles */
        .chat-bubble { max-width: 82%; padding: 8px 10px 8px 14px; border-radius: 12px; position: relative; font-size: 15px; line-height: 1.4; color: var(--wa-text); display: inline-block; word-wrap: break-word; box-shadow: 0 1px 2px rgba(0,0,0,0.3); font-family: 'Outfit', sans-serif; font-weight: 400; z-index: 2; transition: transform 0.2s cubic-bezier(0.2, 0.8, 0.2, 1); }
        .chat-bubble:active { filter: brightness(1.1); }
        
        .chat-bubble.sent { align-self: flex-end; background: var(--wa-sent); border-top-right-radius: 0; margin-left: auto; } 
        .chat-bubble.received { align-self: flex-start; background: var(--wa-received); border-top-left-radius: 0; margin-right: auto; } 
        
        /* Time & Read Receipts */
        .chat-time { display: flex; align-items: center; gap: 5px; font-size: 10px; color: var(--wa-time); float: right; margin: 10px -4px -4px 15px; font-family: 'Outfit'; }
        .msg-status { font-size: 12px; color: #8696a0; }
        .msg-status.read { color: #53bdeb; } /* WhatsApp Blue Tick */
        
        /* Reply Embedded inside Bubble */
        .chat-reply-context { background: rgba(0,0,0,0.25); border-left: 4px solid var(--neon-green); padding: 6px 10px; border-radius: 6px; font-size: 13px; color: #ccc; margin-bottom: 6px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .chat-reply-context .sender { color: var(--neon-green); font-weight: 700; margin-bottom: 2px; font-size: 12px; }
        .received .chat-reply-context { border-left-color: var(--neon-cyan); }
        .received .chat-reply-context .sender { color: var(--neon-cyan); }
        
        /* Reply Banner hovering above input */
        .reply-banner { display: none; background: #202c33; padding: 10px 15px; align-items: center; border-bottom: 1px solid rgba(255,255,255,0.05); z-index: 5; }
        .reply-banner-content { flex-grow: 1; border-left: 4px solid var(--neon-green); padding-left: 12px; background: rgba(0,0,0,0.2); border-radius: 4px 8px 8px 4px; padding-top: 6px; padding-bottom: 6px; }
        .reply-banner-content .rep-title { font-size: 12px; color: var(--neon-green); font-weight: bold; margin-bottom: 3px; }
        .replying-to-text { color: #ccc; font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 90%; }
        .cancel-reply-btn { background: transparent; border: none; color: #8696a0; font-size: 20px; cursor: pointer; padding: 5px 10px; }

        /* =========================================================================
           ADVANCED MODERN PILL INPUT & RECORDING (Based on User Screenshots)
           ========================================================================= */
        .chat-input-container { position: relative; padding: 12px 15px; background: var(--wa-header); z-index: 6; border-top: 1px solid rgba(255,255,255,0.03); }
        
        .chat-modern-pill { display: flex; align-items: center; background: var(--input-bg); border-radius: 30px; padding: 6px 6px; box-shadow: 0 2px 10px rgba(0,0,0,0.2); width: 100%; transition: all 0.3s ease; }
        
        .modern-left-btn { width: 38px; height: 38px; min-width: 38px; border-radius: 50%; background: var(--brand-purple); color: #fff; border: none; display: flex; justify-content: center; align-items: center; font-size: 16px; cursor: pointer; flex-shrink: 0; transition: transform 0.2s ease, box-shadow 0.2s; box-shadow: 0 2px 8px rgba(124, 58, 237, 0.4); margin-right: 10px; }
        .modern-left-btn:active { transform: scale(0.9); }
        
        .modern-input { flex-grow: 1; background: transparent; border: none; color: #fff; font-size: 16px; padding: 5px 0; outline: none; font-family: 'Outfit', sans-serif; min-width: 50px; font-weight: 400; letter-spacing: 0.3px; }
        .modern-input::placeholder { color: var(--icon-gray); font-weight: 300; }
        
        .modern-right-icons { display: flex; align-items: center; gap: 14px; padding: 0 12px 0 8px; color: var(--icon-gray); font-size: 20px; flex-shrink: 0; transition: opacity 0.3s ease, width 0.3s ease, transform 0.3s ease; overflow: hidden; }
        .modern-right-icons.hidden { opacity: 0; width: 0; padding: 0; transform: scale(0.8); pointer-events: none; }
        .modern-right-icons i { cursor: pointer; transition: 0.2s ease; }
        .modern-right-icons i:hover { color: #fff; transform: scale(1.1); }
        .modern-right-icons i:active { transform: scale(0.9); }

        .modern-send-btn { width: 0; height: 38px; border-radius: 20px; background: var(--brand-purple); color: #fff; border: none; display: flex; justify-content: center; align-items: center; font-size: 16px; cursor: pointer; transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); opacity: 0; overflow: hidden; box-shadow: 0 2px 8px rgba(124, 58, 237, 0.4); }
        .modern-send-btn.active { width: 50px; opacity: 1; margin-left: 8px; }
        .modern-send-btn:active { transform: scale(0.9); }
        .modern-send-btn i { transform: translateX(-1px); }

        /* Voice Recording UI */
        .recording-ui { display: none; align-items: center; justify-content: space-between; width: 100%; background: #2a3942; border-radius: 30px; padding: 6px 8px; box-shadow: inset 0 2px 5px rgba(0,0,0,0.2); }
        .blinking-dot { width: 12px; height: 12px; background: var(--danger); border-radius: 50%; display: inline-block; animation: blink 1s infinite; margin-right: 8px; }
        @keyframes blink { 0% { opacity: 1; } 50% { opacity: 0.3; } 100% { opacity: 1; } }
        .rec-timer { color: var(--danger); font-family: 'Orbitron', monospace; font-size: 14px; font-weight: bold; flex-grow: 1; padding-left: 5px; }

        /* Sticker Drawer */
        .sticker-drawer { position: absolute; bottom: 70px; left: 15px; right: 15px; background: #1f2937; border-radius: 16px; padding: 15px; box-shadow: 0 15px 40px rgba(0,0,0,0.6); opacity: 0; transform: translateY(20px) scale(0.95); pointer-events: none; transition: all 0.3s cubic-bezier(0.2, 0.8, 0.2, 1); z-index: 10; border: 1px solid rgba(255,255,255,0.05); }
        .sticker-drawer.show { opacity: 1; transform: translateY(0) scale(1); pointer-events: auto; }
        .sticker-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; max-height: 200px; overflow-y: auto; padding: 5px; }
        .sticker-grid img { width: 100%; height: auto; cursor: pointer; transition: 0.2s; border-radius: 8px; filter: drop-shadow(0 2px 5px rgba(0,0,0,0.3)); }
        .sticker-grid img:hover { transform: scale(1.15); }

        /* Attachment Menu Drawer */
        .attachment-drawer { position: absolute; bottom: 70px; left: 15px; right: 15px; background: #1f2937; border-radius: 16px; padding: 20px; box-shadow: 0 15px 40px rgba(0,0,0,0.6); display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; opacity: 0; transform: translateY(20px) scale(0.95); pointer-events: none; transition: all 0.3s cubic-bezier(0.2, 0.8, 0.2, 1); z-index: 10; border: 1px solid rgba(255,255,255,0.05); }
        .attachment-drawer.show { opacity: 1; transform: translateY(0) scale(1); pointer-events: auto; }
        .attach-item { display: flex; flex-direction: column; align-items: center; gap: 8px; cursor: pointer; transition: 0.2s; }
        .attach-item:hover { transform: translateY(-3px); }
        .attach-icon-circle { width: 50px; height: 50px; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 20px; color: #fff; box-shadow: 0 5px 15px rgba(0,0,0,0.3); }
        .attach-item span { font-size: 11px; color: #d1d5db; font-family: 'Outfit'; font-weight: 500; }
        
        /* Attachment Colors */
        .bg-doc { background: #6366f1; } .bg-cam { background: #ec4899; } .bg-gal { background: #a855f7; }
        .bg-aud { background: #f97316; } .bg-loc { background: #10b981; } .bg-con { background: #3b82f6; }

        /* =========================================================================
           LONG PRESS MODAL OVERLAY (NATIVE CONTEXT MENU)
           ========================================================================= */
        .long-press-overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.6); z-index: 99999; backdrop-filter: blur(2px); justify-content: center; align-items: center; }
        .long-press-modal { background: #233138; border-radius: 12px; width: 85%; max-width: 320px; overflow: hidden; box-shadow: 0 15px 50px rgba(0,0,0,0.5); transform: scale(0.9); opacity: 0; transition: all 0.2s cubic-bezier(0.2, 0.8, 0.2, 1); }
        .long-press-overlay.active { display: flex; }
        .long-press-overlay.active .long-press-modal { transform: scale(1); opacity: 1; }
        
        .msg-info-pane { padding: 15px 20px; background: rgba(0,0,0,0.2); border-bottom: 1px solid rgba(255,255,255,0.05); }
        .msg-info-row { display: flex; justify-content: space-between; font-size: 13px; color: #8696a0; margin-bottom: 6px; }
        .msg-info-row:last-child { margin-bottom: 0; }
        .msg-info-row span.val { color: #e9edef; font-weight: 500; }
        
        .modal-action-btn { width: 100%; padding: 15px 20px; text-align: left; background: transparent; border: none; border-bottom: 1px solid rgba(255,255,255,0.05); color: #e9edef; font-size: 16px; cursor: pointer; display: flex; align-items: center; gap: 15px; transition: 0.2s; font-family: 'Outfit'; }
        .modal-action-btn:last-child { border-bottom: none; }
        .modal-action-btn:active { background: rgba(255,255,255,0.05); }
        .modal-action-btn i { font-size: 18px; width: 25px; text-align: center; }
        .modal-action-btn.delete { color: #ef4444; }

        /* =========================================================================
           TIMELINE, HISTORY & MAP
           ========================================================================= */
        .client-list-item { background: rgba(0,0,0,0.6); padding: 15px; border-radius: 10px; border-left: 4px solid var(--neon-cyan); margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.3); }
        .client-list-item:hover { background: rgba(212, 175, 55, 0.1); border-left-color: var(--gold); transform: translateY(-2px); }
        .client-list-item h4 { margin:0 0 4px 0; font-size: 15px; color: #fff; font-family: 'Cinzel', serif; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .client-list-item p { margin:0; font-size: 12px; color: #888; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .client-list-actions { display: flex; align-items: center; gap: 12px; }
        .client-list-pin { font-family: 'Orbitron', monospace; font-size: 16px; color: var(--gold); font-weight: bold; background: rgba(212,175,55,0.1); padding: 6px 10px; border-radius: 6px; letter-spacing: 2px; }
        
        .action-icon-btn { background: transparent; border: none; color: #888; font-size: 18px; cursor: pointer; transition: 0.3s; padding: 5px; }
        .action-icon-btn:hover { color: var(--neon-cyan); transform: scale(1.1); }
        .action-icon-btn.trash { color: rgba(255,51,51,0.7); }
        .action-icon-btn.trash:hover { color: var(--danger); transform: scale(1.2); }

        .map-wrapper { width: 100%; height: 260px; border-radius: 12px; overflow: hidden; border: 2px solid #222; position: relative; background: #050505; margin-bottom: 15px; }
        .map-iframe { width: 100%; height: 100%; border: none; filter: invert(100%) hue-rotate(180deg) brightness(85%) contrast(110%) sepia(30%); pointer-events: none; transition: 1s ease; opacity: 0; }
        .map-iframe.loaded { opacity: 1; }
        .map-loader { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: var(--gold); font-size: 13px; text-align: center; font-weight: bold; letter-spacing: 1px; z-index: 1; }
        
        .location-display { background: rgba(0,229,255,0.05); border: 1px solid rgba(0,229,255,0.2); border-radius: 8px; padding: 12px; margin-bottom: 15px; display: flex; align-items: center; gap: 12px; }
        .location-icon { width: 35px; height: 35px; border-radius: 50%; background: rgba(0,229,255,0.15); display: flex; justify-content: center; align-items: center; color: var(--neon-cyan); font-size: 16px; }
        .location-text h4 { margin: 0 0 3px 0; font-size: 10px; color: #888; text-transform: uppercase; letter-spacing: 1px; }
        .location-text p { margin: 0; font-size: 15px; color: #fff; font-weight: 700; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 250px; }

        .telemetrics-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-bottom: 12px; }
        .metric-box { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.1); border-radius: 8px; padding: 10px; text-align: center; }
        .metric-label { font-size: 10px; color: #888; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 5px; }
        .metric-value { font-family: 'Orbitron', sans-serif; font-size: 14px; font-weight: 700; color: var(--neon-cyan); }

        .timeline-feed { padding-left: 20px; position: relative; flex-grow: 1; overflow-y: auto; padding-right: 5px; min-height: 200px; margin-bottom: 15px; }
        .timeline-item { position: relative; margin-bottom: 20px; padding-bottom: 20px; border-bottom: 1px dashed rgba(255,255,255,0.05); animation: slideInLeft 0.4s ease forwards; display: flex; justify-content: space-between; align-items: flex-start; }
        .timeline-item:last-child { border-bottom: none; margin-bottom: 0; padding-bottom: 0; }
        .timeline-item::before { content: ''; position: absolute; left: -26px; top: 3px; width: 12px; height: 12px; border-radius: 50%; background: var(--neon-green); box-shadow: 0 0 10px var(--neon-green); border: 2px solid #111; z-index: 2; }
        .timeline-item::after { content: ''; position: absolute; left: -21px; top: 15px; width: 2px; height: 100%; background: rgba(0, 250, 154, 0.3); z-index: 1; }
        .timeline-item:last-child::after { display: none; }
        
        .update-content { width: 85%; }
        .update-time { font-size: 10px; color: var(--gold); margin-bottom: 5px; font-family: 'Orbitron', sans-serif; font-weight: bold; text-transform: uppercase; letter-spacing: 1px; }
        .update-text { font-size: 14px; color: #eee; line-height: 1.5; font-weight: 500; word-wrap: break-word; }

        .quick-actions { display: flex; gap: 8px; margin-bottom: 15px; flex-wrap: wrap; }
        .quick-btn { background: rgba(255,255,255,0.05); border: 1px solid rgba(212,175,55,0.4); color: #fff; padding: 8px 12px; border-radius: 8px; font-size: 11px; cursor: pointer; transition: 0.3s; font-family: 'Orbitron', sans-serif; flex-grow: 1; text-align: center; }
        .quick-btn:hover { background: rgba(212,175,55,0.2); border-color: var(--gold); color: var(--gold); }
        
        .target-lock-banner { background: rgba(0, 229, 255, 0.1); border: 1px dashed var(--neon-cyan); padding: 12px 15px; border-radius: 8px; margin-bottom: 20px; display: flex; align-items: center; gap: 15px; flex-shrink: 0; width: 100%; max-width: 600px; }
        .target-lock-banner i { color: var(--neon-cyan); font-size: 24px; animation: targetPulse 2s infinite; }
        .target-lock-details h4 { margin: 0; color: var(--neon-cyan); font-size: 14px; font-family: 'Orbitron'; letter-spacing: 1px; text-transform: uppercase; }
        .target-lock-details p { margin: 3px 0 0 0; color: #fff; font-size: 12px; }
        @keyframes targetPulse { 0%, 100% { transform: scale(1); opacity: 1; } 50% { transform: scale(1.1); opacity: 0.5; } }
    </style>
</head>
<body> 

    <!-- JS GENERATED MANIFEST INJECTED AUTOMATICALLY FOR STANDALONE PWA -->

    <!-- PWA INSTALL BANNER -->
    <div id="install-banner" class="install-banner">
        <div style="display:flex; align-items:center; gap:12px;">
            <img src="https://cdn-icons-png.flaticon.com/512/814/814513.png" style="width:40px; height:40px; border-radius:10px; box-shadow: 0 4px 10px rgba(0,0,0,0.5);">
            <div>
                <h4 style="margin:0; color:var(--gold); font-size:14px; font-family:'Cinzel';">MND Tracking App</h4>
                <p style="margin:0; color:#aaa; font-size:11px;">Install for full-screen experience</p>
            </div>
        </div>
        <div style="display:flex; align-items:center; gap:8px;">
            <button class="mn-btn btn-gold" style="padding:8px 15px; width:auto; font-size:12px; margin:0;" onclick="installApp()">INSTALL</button>
            <button style="background:transparent; border:none; color:#888; font-size:20px; padding:5px; cursor:pointer;" onclick="closeInstallBanner()"><i class="fas fa-times"></i></button>
        </div>
    </div>

    <!-- GLOBAL LONG PRESS MODAL -->
    <div id="msg-action-overlay" class="long-press-overlay" onclick="closeMsgModal()">
        <div class="long-press-modal" onclick="event.stopPropagation()">
            <div class="msg-info-pane">
                <div class="msg-info-row"><span>Sent:</span> <span class="val" id="modal-time-sent">--</span></div>
                <div class="msg-info-row"><span>Status:</span> <span class="val" id="modal-time-read">--</span></div>
            </div>
            <button class="modal-action-btn" onclick="execModalReply()"><i class="fas fa-reply"></i> Reply</button>
            <button class="modal-action-btn" onclick="execModalCopy()"><i class="fas fa-copy"></i> Copy Text</button>
            <button class="modal-action-btn delete" id="modal-btn-delete" onclick="execModalDelete()"><i class="fas fa-trash"></i> Delete Message</button>
        </div>
    </div>

    <!-- HIDDEN FILE INPUTS FOR ATTACHMENTS -->
    <input type="file" id="file-gallery" accept="image/*,video/*" style="display:none;" onchange="handleFileUpload(event, 'image')">
    <input type="file" id="file-doc" accept=".pdf,.doc,.docx,.txt,.xls,.xlsx" style="display:none;" onchange="handleFileUpload(event, 'document')">
    <input type="file" id="file-audio" accept="audio/*" style="display:none;" onchange="handleFileUpload(event, 'audio')">
    <input type="file" id="file-camera" accept="image/*" capture="environment" style="display:none;" onchange="handleFileUpload(event, 'camera')">

    <div class="bg-map"></div>
    <div class="grid-overlay"></div>
    <div id="toast-container"></div>

    <!-- ========================================================================= -->
    <!-- VIEW 1: GATEKEEPER -->
    <!-- ========================================================================= -->
    <div id="view-gatekeeper" class="view-container active-view" style="justify-content: center;">
        <div class="gatekeeper-box" style="background: rgba(15,15,20,0.85); position:relative; overflow:hidden; border-radius:20px; padding:40px 25px; box-shadow: 0 20px 60px rgba(0,0,0,0.9); border:2px solid var(--gold); max-width:450px;">
            <h1 class="app-title"><i class="fas fa-satellite-dish"></i> MND TRACKING</h1>
            <p class="app-subtitle">Secured Cloud Logistics Portal.<br>Enter your Mobile Number and PIN below. Send in your Register Whatsapp Number</p>
            
            <div class="input-group">
                <i class="fas fa-phone-alt"></i>
                <input type="tel" id="login-phone" class="mn-input" placeholder="Mobile Number *" autocomplete="off">
            </div>
            
            <div class="input-group">
                <input type="password" id="login-pin" class="mn-input pin-style" placeholder="6 DIGIT PIN" maxlength="6" autocomplete="off">
            </div>

            <button class="mn-btn btn-gold" id="login-btn" onclick="processLogin(event)"><i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE</button>
            <p id="login-error" style="display:none; color:var(--danger); font-size:13px; font-weight:bold; margin-top:15px;"></p>
        </div>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 2: ADMIN DISPATCH CENTER (MAIN) -->
    <!-- ========================================================================= -->
    <div id="view-admin" class="view-container">
        <div class="dash-header">
            <div>
                <h2 style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:22px;">Command Center</h2>
                <span style="font-size:12px; color:#888;">Authorized Personnel Only</span>
            </div>
            <div class="status-badge"><i class="fas fa-link"></i> DB SYNCED</div>
        </div>

        <div class="card">
            <h3><i class="fas fa-key"></i> Provision / Update Client</h3>
            <p style="font-size:11px; color:#aaa; margin-bottom:15px;">Entering an existing number will elegantly UPDATE their PIN without deleting history.</p>
            
            <div style="display:flex; gap:10px;">
                <input type="text" id="admin-c-name" class="mn-input" style="padding:12px; margin-bottom:12px;" placeholder="Client Name *">
                <input type="tel" id="admin-c-phone" class="mn-input" style="padding:12px; margin-bottom:12px;" placeholder="Mobile No. *">
            </div>
            
            <div style="display:flex; gap:10px;">
                <input type="text" id="admin-c-event" class="mn-input" style="flex:2; padding:12px; margin-bottom:15px;" placeholder="Event Ref *">
                <input type="text" id="admin-c-custom-pin" class="mn-input" style="flex:1; padding:12px; margin-bottom:15px; font-family:'Orbitron'; text-align:center; letter-spacing:4px; color:var(--neon-cyan);" placeholder="Custom PIN" maxlength="6">
            </div>
            
            <button class="mn-btn btn-gold" id="btn-generate" onclick="adminGeneratePin(event)"><i class="fas fa-microchip"></i> AUTHORIZE & SAVE TO CLOUD</button>

            <div id="admin-pin-result" style="display:none; margin-top:20px; text-align:center; background: rgba(0,0,0,0.6); padding: 20px; border-radius: 12px; border: 1px dashed var(--neon-green);">
                <p style="color:#aaa; font-size:12px; text-transform:uppercase; letter-spacing:1px;">Client Access Ready:</p>
                <div style="font-family:'Orbitron', monospace; font-size:40px; color:var(--neon-green); font-weight:900; letter-spacing:10px; margin:10px 0; text-shadow: 0 0 20px rgba(0,250,154,0.4);" id="display-new-pin">000000</div>
                <button class="mn-btn btn-green" onclick="adminShareWhatsApp(event)"><i class="fab fa-whatsapp"></i> DISPATCH VIA WHATSAPP</button>
            </div>
        </div>

        <div class="card flex-grow">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom: 5px; flex-shrink: 0;">
                <h3 style="margin:0; border:none; padding:0;"><i class="fas fa-history"></i> Live Network Roster</h3>
                <span style="font-size:11px; background:rgba(212,175,55,0.1); padding:4px 8px; border-radius:4px; color:var(--gold);">Auto-Sync</span>
            </div>
            <p style="font-size:11px; color:#888; margin-bottom:15px; flex-shrink:0;">Click any client below to open their Targeted Live Session for GPS & Comms.</p>
            
            <div id="admin-active-list" style="flex-grow:1; overflow-y:auto; padding-right: 5px;">
                <div style="text-align:center; color:#555; font-size:13px; padding:20px;"><i class="fas fa-circle-notch fa-spin"></i> Fetching history logs...</div>
            </div>
        </div>

        <button class="mn-btn btn-danger" onclick="systemLogout(event)"><i class="fas fa-sign-out-alt"></i> TERMINATE SYSTEM</button>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 3: ADMIN TARGETED SESSION -->
    <!-- ========================================================================= -->
    <div id="view-admin-session" class="view-container">
        <div class="dash-header" style="border-color:var(--neon-cyan);">
            <div>
                <h2 style="color:var(--neon-cyan); font-family:'Orbitron'; margin:0; font-size:20px;">Targeted Session</h2>
                <span style="font-size:12px; color:#888;">Direct Encrypted Link</span>
            </div>
            <button class="action-icon-btn" onclick="closeAdminSession(event)" style="color:var(--gold); border:1px solid var(--gold); border-radius:50%; width:35px; height:35px; background:rgba(212,175,55,0.1);"><i class="fas fa-times"></i></button>
        </div>

        <div class="target-lock-banner">
            <i class="fas fa-crosshairs"></i>
            <div class="target-lock-details">
                <h4>Active Target Locked</h4>
                <p><span id="session-c-name" style="font-weight:bold;">Name</span> | <span id="session-c-event">Event</span> | <span id="session-c-phone" style="color:var(--gold);">Phone</span></p>
            </div>
        </div>

        <!-- 📍 Client Pinned Location -->
        <div class="card" id="admin-client-loc-card" style="display:none; border-color:var(--neon-green); background:rgba(0,250,154,0.05);">
            <h3 style="color:var(--neon-green); border-bottom-color:rgba(0,250,154,0.3);"><i class="fas fa-street-view"></i> Target Event Location</h3>
            <p style="font-size:12px; color:#aaa; margin-bottom:15px;">The client has shared their exact event coordinates.</p>
            <a id="admin-view-client-map" href="#" target="_blank" class="mn-btn btn-green" style="font-size:13px; padding:12px; text-decoration:none;"><i class="fas fa-map-marker-alt"></i> OPEN CLIENT LOCATION IN MAPS</a>
        </div>

        <!-- Session: GPS Telemetry -->
        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-satellite"></i> Direct GPS Uplink</h3>
            <div class="telemetrics-grid" id="admin-telemetrics" style="display:none;">
                <div class="metric-box"><div class="metric-label">Speed</div><div class="metric-value" id="a-speed">0 km/h</div></div>
                <div class="metric-box"><div class="metric-label">Accuracy</div><div class="metric-value" id="a-acc">0 m</div></div>
                <div class="metric-box"><div class="metric-label">Pings</div><div class="metric-value" id="a-ping">0</div></div>
            </div>
            
            <div id="admin-ai-location" class="location-display" style="display:none; border-color:var(--neon-cyan); background:rgba(0,229,255,0.05);">
                <div class="location-icon" style="color:var(--neon-cyan); background:rgba(0,229,255,0.1);"><i class="fas fa-map-marked-alt"></i></div>
                <div class="location-text">
                    <h4>Current Area Detected</h4>
                    <p id="a-loc-name">Resolving coordinates...</p>
                </div>
            </div>

            <button class="mn-btn btn-dark" style="border-color:var(--neon-cyan); color:var(--neon-cyan);" id="btn-broadcast" onclick="toggleLocationBroadcast(event)"><i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET</button>
        </div>

        <!-- Session: Push Update (Public Timeline) -->
        <div class="card">
            <h3 style="color:var(--neon-cyan); border-bottom-color:rgba(0, 229, 255, 0.3);"><i class="fas fa-bullhorn"></i> Public Logistics Feed</h3>
            
            <div class="quick-actions">
                <button class="quick-btn" onclick="setQuickUpdate('🚚 Fleet Dispatched. En route to venue.', event)"><i class="fas fa-truck-moving"></i> Dispatched</button>
                <button class="quick-btn" onclick="setQuickUpdate('📍 Fleet Arrived. Commencing Unloading.', event)"><i class="fas fa-map-marker-alt"></i> Arrived</button>
                <button class="quick-btn" onclick="setQuickUpdate('⚙️ Unloaded. Stage & Audio setup begun.', event)"><i class="fas fa-tools"></i> Setup</button>
                <button class="quick-btn" style="border-color:rgba(255,51,51,0.5); color:#ff6b6b;" onclick="setQuickUpdate('🛑 Minor delay due to traffic. Actively moving.', event)"><i class="fas fa-traffic-light"></i> Delay</button>
            </div>

            <textarea id="admin-update-text" class="mn-input" rows="2" style="resize:none; padding:15px; flex-shrink:0;" placeholder="Push an official update to the client's public timeline..."></textarea>
            <button class="mn-btn btn-gold" style="margin-top:12px; flex-shrink:0; background: linear-gradient(135deg, var(--neon-cyan) 0%, #0088cc 100%); box-shadow: 0 5px 25px rgba(0,229,255,0.3);" id="btn-push-update" onclick="adminPushUpdate(event)"><i class="fas fa-paper-plane"></i> TRANSMIT TO TIMELINE</button>
            
            <div style="border-top: 1px dashed rgba(255,255,255,0.1); padding-top:15px; margin-top: 15px;">
                <h4 style="font-size:12px; color:#aaa; margin-bottom:10px; text-transform:uppercase; flex-shrink:0;">Manage Sent Updates:</h4>
                <div id="admin-manage-updates-area" class="timeline-feed" style="max-height: 200px;">
                    <!-- Updates populate here -->
                </div>
            </div>
        </div>

        <!-- 💬 ADVANCED TWO-WAY WHATSAPP CHAT (ADMIN SIDE) -->
        <div class="chat-card">
            <div class="chat-header">
                <div style="display:flex; align-items:center; gap:12px;">
                    <div style="width:40px; height:40px; border-radius:50%; background:var(--neon-cyan); display:flex; justify-content:center; align-items:center; color:#000; font-size:18px;"><i class="fas fa-user-tie"></i></div>
                    <div>
                        <h3 style="margin:0; padding:0; color:#fff; font-size:16px; border:none;" id="admin-chat-header-name">Target Client</h3>
                        <div style="font-size:11px; color:var(--neon-green);">Online</div>
                    </div>
                </div>
                <span style="font-size:10px; color:var(--neon-cyan); border:1px solid var(--neon-cyan); padding:3px 8px; border-radius:12px;"><i class="fas fa-lock"></i> Encrypted</span>
            </div>
            
            <!-- Chat Render Area -->
            <div id="admin-chat-area" class="chat-area" onclick="closeAllMenus()">
                <div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>
            </div>

            <div id="admin-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="admin-reply-sender">Client Name</div>
                    <div class="replying-to-text" id="admin-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('admin', event)"><i class="fas fa-times"></i></button>
            </div>

            <!-- PERFECT PILL INPUT FOR ADMIN -->
            <div class="chat-input-container">
                <!-- Sticker Drawer -->
                <div id="admin-sticker-menu" class="sticker-drawer">
                    <div class="sticker-grid">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190411.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190406.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190412.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140061.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190413.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140048.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140047.png" onclick="sendSticker('admin', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140034.png" onclick="sendSticker('admin', this.src, event)">
                    </div>
                </div>

                <!-- Attachment Drawer -->
                <div id="admin-attach-menu" class="attachment-drawer">
                    <div class="attach-item" onclick="triggerFileInput('file-doc', 'admin')"><div class="attach-icon-circle bg-doc"><i class="fas fa-file-alt"></i></div><span>Document</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-camera', 'admin')"><div class="attach-icon-circle bg-cam"><i class="fas fa-camera"></i></div><span>Camera</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-gallery', 'admin')"><div class="attach-icon-circle bg-gal"><i class="fas fa-image"></i></div><span>Gallery</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-audio', 'admin')"><div class="attach-icon-circle bg-aud"><i class="fas fa-headphones"></i></div><span>Audio</span></div>
                    <div class="attach-item" onclick="sendLocationMessage('admin', event)"><div class="attach-icon-circle bg-loc"><i class="fas fa-map-marker-alt"></i></div><span>Location</span></div>
                    <div class="attach-item" onclick="sendContactMessage('admin', event)"><div class="attach-icon-circle bg-con"><i class="fas fa-user"></i></div><span>Contact</span></div>
                </div>

                <!-- Default Text Input Pill -->
                <div class="chat-modern-pill" id="admin-default-pill">
                    <button class="modern-left-btn" onclick="toggleAttachMenu('admin', event)">
                        <i class="fas fa-camera" id="admin-left-icon"></i>
                    </button>
                    
                    <input type="text" id="admin-chat-input" class="modern-input" placeholder="Message Client..." oninput="handleInputTyping('admin')">
                    
                    <div class="modern-right-icons" id="admin-right-icons">
                        <i class="fas fa-microphone" onclick="startRecording('admin')"></i>
                        <i class="fas fa-image" onclick="triggerFileInput('file-gallery', 'admin')"></i>
                        <i class="far fa-sticky-note" onclick="toggleStickerMenu('admin', event)"></i>
                        <i class="fas fa-plus-circle" onclick="toggleAttachMenu('admin', event)"></i>
                    </div>
                    
                    <button id="admin-send-btn" class="modern-send-btn" onclick="adminSendChatMessage(event)">
                        <i class="fas fa-paper-plane"></i>
                    </button>
                </div>

                <!-- Recording UI Pill -->
                <div class="recording-ui" id="admin-recording-pill">
                    <div style="display:flex; align-items:center;">
                        <span class="blinking-dot"></span> <span class="rec-timer" id="admin-record-time">00:00</span>
                    </div>
                    <div style="display:flex; align-items:center; gap:10px;">
                        <button class="modern-left-btn" style="background:#555;" onclick="cancelRecording('admin')"><i class="fas fa-trash"></i></button>
                        <button class="modern-send-btn active" style="width:40px; margin:0;" onclick="stopAndSendRecording('admin')"><i class="fas fa-paper-plane"></i></button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- ========================================================================= -->
    <!-- VIEW 4: CLIENT LIVE DASHBOARD -->
    <!-- ========================================================================= -->
    <div id="view-client" class="view-container" style="padding: 20px 10px;">
        <div class="dash-header" style="max-width:100%; width:100%;">
            <div style="width:70%;">
                <h2 id="client-dash-event" style="color:var(--gold); font-family:'Cinzel'; margin:0; font-size:20px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">Loading Event...</h2>
                <span style="font-size:12px; color:#888;">Authorized: <span id="client-dash-name" style="color:#fff;">Loading...</span></span>
            </div>
            <div class="status-badge" id="client-conn-status"><i class="fas fa-circle fa-beat"></i> ONLINE</div>
        </div>

        <!-- 📍 Client Send Location to HQ -->
        <div class="card" style="max-width:100%; width:100%; padding: 15px; background:rgba(0, 250, 154, 0.05); border-color:var(--neon-green); margin-bottom:15px; flex-shrink:0;">
            <div style="display:flex; justify-content:space-between; align-items:center;">
                <div>
                    <h4 style="color:var(--neon-green); margin:0; font-size:14px;"><i class="fas fa-street-view"></i> Event Location</h4>
                    <p style="font-size:11px; color:#aaa; margin:0;">Send exact map pin to the logistics team.</p>
                </div>
                <button class="mn-btn btn-green" style="width:auto; padding: 10px 15px; font-size:12px;" onclick="clientShareLocation(event)"><i class="fas fa-share-location"></i> SHARE</button>
            </div>
        </div>

        <!-- Map & Telemetrics Area -->
        <div class="card" style="max-width:100%; width:100%; padding: 15px; flex-shrink:0; margin-bottom:15px;">
            <div style="display:flex; justify-content:space-between; align-items:center; padding-bottom: 12px; border-bottom: 1px dashed rgba(255,255,255,0.1); margin-bottom: 10px;">
                <h3 style="margin:0; border:none; padding:0; font-size:16px;"><i class="fas fa-crosshairs"></i> Live Target Tracking</h3>
                <span id="client-gps-time" style="font-size:11px; color:#888; font-family:'Orbitron';">Awaiting Signal...</span>
            </div>
            
            <div class="telemetrics-grid">
                <div class="metric-box"><div class="metric-label">Speed</div><div class="metric-value" id="c-speed">--</div></div>
                <div class="metric-box"><div class="metric-label">Heading</div><div class="metric-value" id="c-heading">--</div></div>
                <div class="metric-box"><div class="metric-label">Signal</div><div class="metric-value" id="c-accuracy" style="color:var(--gold);">--</div></div>
            </div>

            <div class="map-wrapper">
                <div class="map-loader" id="map-loader-text"><i class="fas fa-satellite-dish fa-spin fa-2x" style="margin-bottom:10px;"></i><br>SEARCHING SATELLITE...</div>
                <iframe id="client-map-iframe" class="map-iframe" src="" onload="this.classList.add('loaded')"></iframe>
            </div>

            <!-- AI Location Appears Below Map -->
            <div class="location-display" id="client-location-display" style="display:none;">
                <div class="location-icon"><i class="fas fa-map-marker-alt"></i></div>
                <div class="location-text">
                    <h4>Nearest Detected Area</h4>
                    <p id="c-location-name">Syncing AI Location...</p>
                </div>
            </div>
            
            <a id="ext-map-link" href="#" target="_blank" class="mn-btn btn-green" style="font-size:13px; padding:14px; text-decoration:none; display:none;"><i class="fas fa-external-link-alt"></i> OPEN IN GOOGLE MAPS APP</a>
        </div>

        <!-- Premium Timeline Updates Area -->
        <div class="card" style="max-width:100%; width:100%; display:flex; flex-direction:column; flex-shrink:0;">
            <h3 style="margin-bottom: 20px; flex-shrink:0;"><i class="fas fa-bullhorn"></i> Official Logistics Updates</h3>
            <div id="client-updates-area" class="timeline-feed" style="max-height: 250px;">
                <div style="text-align:center; color:#555; font-size:13px; padding:20px; border:none; margin-left:-20px;"><i class="fas fa-circle-notch fa-spin"></i> Listening for HQ timeline broadcasts...</div>
            </div>
        </div>

        <!-- 💬 ADVANCED TWO-WAY WHATSAPP CHAT (CLIENT SIDE) -->
        <div class="chat-card">
            <div class="chat-header">
                <div style="display:flex; align-items:center; gap:12px;">
                    <div style="width:40px; height:40px; border-radius:50%; background:var(--gold); display:flex; justify-content:center; align-items:center; color:#000; font-size:18px;"><i class="fas fa-headset"></i></div>
                    <div>
                        <h3 style="margin:0; padding:0; color:#fff; font-size:16px; border:none;">HQ Dispatch Support</h3>
                        <div style="font-size:11px; color:var(--neon-green);">Online</div>
                    </div>
                </div>
                <span style="font-size:10px; color:var(--gold); border:1px solid var(--gold); padding:3px 8px; border-radius:12px;"><i class="fas fa-lock"></i> Encrypted</span>
            </div>
            
            <div id="client-chat-area" class="chat-area" onclick="closeAllMenus()">
                <div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>
            </div>

            <div id="client-reply-banner" class="reply-banner">
                <div class="reply-banner-content">
                    <div class="rep-title" id="client-reply-sender">HQ Dispatch</div>
                    <div class="replying-to-text" id="client-reply-text">Replying to...</div>
                </div>
                <button class="cancel-reply-btn" onclick="cancelReply('client', event)"><i class="fas fa-times"></i></button>
            </div>

            <!-- PERFECT PILL INPUT FOR CLIENT -->
            <div class="chat-input-container">
                <!-- Sticker Drawer -->
                <div id="client-sticker-menu" class="sticker-drawer">
                    <div class="sticker-grid">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190411.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190406.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190412.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140061.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/190/190413.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140048.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140047.png" onclick="sendSticker('client', this.src, event)">
                        <img src="https://cdn-icons-png.flaticon.com/512/4140/4140034.png" onclick="sendSticker('client', this.src, event)">
                    </div>
                </div>

                <!-- Attachment Drawer -->
                <div id="client-attach-menu" class="attachment-drawer">
                    <div class="attach-item" onclick="triggerFileInput('file-doc', 'client')"><div class="attach-icon-circle bg-doc"><i class="fas fa-file-alt"></i></div><span>Document</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-camera', 'client')"><div class="attach-icon-circle bg-cam"><i class="fas fa-camera"></i></div><span>Camera</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-gallery', 'client')"><div class="attach-icon-circle bg-gal"><i class="fas fa-image"></i></div><span>Gallery</span></div>
                    <div class="attach-item" onclick="triggerFileInput('file-audio', 'client')"><div class="attach-icon-circle bg-aud"><i class="fas fa-headphones"></i></div><span>Audio</span></div>
                    <div class="attach-item" onclick="sendLocationMessage('client', event)"><div class="attach-icon-circle bg-loc"><i class="fas fa-map-marker-alt"></i></div><span>Location</span></div>
                    <div class="attach-item" onclick="sendContactMessage('client', event)"><div class="attach-icon-circle bg-con"><i class="fas fa-user"></i></div><span>Contact</span></div>
                </div>

                <!-- Default Text Input Pill -->
                <div class="chat-modern-pill" id="client-default-pill">
                    <button class="modern-left-btn" onclick="toggleAttachMenu('client', event)">
                        <i class="fas fa-camera" id="client-left-icon"></i>
                    </button>
                    
                    <input type="text" id="client-chat-input" class="modern-input" placeholder="Message HQ..." oninput="handleInputTyping('client')">
                    
                    <div class="modern-right-icons" id="client-right-icons">
                        <i class="fas fa-microphone" onclick="startRecording('client')"></i>
                        <i class="fas fa-image" onclick="triggerFileInput('file-gallery', 'client')"></i>
                        <i class="far fa-sticky-note" onclick="toggleStickerMenu('client', event)"></i>
                        <i class="fas fa-plus-circle" onclick="toggleAttachMenu('client', event)"></i>
                    </div>
                    
                    <button id="client-send-btn" class="modern-send-btn" onclick="clientSendChatMessage(event)">
                        <i class="fas fa-paper-plane"></i>
                    </button>
                </div>

                <!-- Recording UI Pill -->
                <div class="recording-ui" id="client-recording-pill">
                    <div style="display:flex; align-items:center;">
                        <span class="blinking-dot"></span> <span class="rec-timer" id="client-record-time">00:00</span>
                    </div>
                    <div style="display:flex; align-items:center; gap:10px;">
                        <button class="modern-left-btn" style="background:#555;" onclick="cancelRecording('client')"><i class="fas fa-trash"></i></button>
                        <button class="modern-send-btn active" style="width:40px; margin:0;" onclick="stopAndSendRecording('client')"><i class="fas fa-paper-plane"></i></button>
                    </div>
                </div>
            </div>
        </div>

        <button class="mn-btn btn-danger" style="max-width:100%; width:100%; flex-shrink:0;" onclick="systemLogout(event)"><i class="fas fa-power-off"></i> DISCONNECT TERMINAL</button>
    </div>

    <!-- ========================================================================= -->
    <!-- JAVASCRIPT & FIREBASE ENGINE -->
    <!-- ========================================================================= -->
    <script>
        // --- PWA & APP INSTALL LOGIC ---
        const manifestData = {
            "name": "MND Logistics Live Tracking",
            "short_name": "MND Track",
            "start_url": ".",
            "display": "standalone",
            "background_color": "#050508",
            "theme_color": "#050508",
            "icons": [{"src": "https://cdn-icons-png.flaticon.com/512/814/814513.png", "sizes": "512x512", "type": "image/png"}]
        };
        const manifestBlob = new Blob([JSON.stringify(manifestData)], {type: 'application/manifest+json'});
        const manifestLink = document.createElement('link');
        manifestLink.rel = 'manifest';
        manifestLink.href = URL.createObjectURL(manifestBlob);
        document.head.appendChild(manifestLink);

        let deferredPrompt;
        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault();
            deferredPrompt = e;
            // Only show banner if not already in standalone app mode
            if (!window.matchMedia('(display-mode: standalone)').matches) {
                document.getElementById('install-banner').classList.add('show');
            }
        });

        function installApp() {
            if (deferredPrompt) {
                deferredPrompt.prompt();
                deferredPrompt.userChoice.then((choiceResult) => {
                    deferredPrompt = null;
                    document.getElementById('install-banner').classList.remove('show');
                });
            }
        }
        function closeInstallBanner() {
            document.getElementById('install-banner').classList.remove('show');
        }

        // --- SAFE STRING ESCAPING (CRITICAL FIX FOR CRASHES) ---
        function escapeHTML(str) {
            if (!str) return "";
            return String(str).replace(/[&<>'"]/g, 
                tag => ({
                    '&': '&amp;',
                    '<': '&lt;',
                    '>': '&gt;',
                    "'": '&#39;',
                    '"': '&quot;'
                }[tag] || tag)
            );
        }

        // --- Global Click Listener for Menus ---
        function closeAllMenus() {
            document.querySelectorAll('.attachment-drawer').forEach(d => d.classList.remove('show'));
            document.querySelectorAll('.sticker-drawer').forEach(d => d.classList.remove('show'));
        }

        // --- MODERN PILL INPUT LOGIC (From Screenshots) ---
        function handleInputTyping(role) {
            const input = document.getElementById(role + '-chat-input');
            const rightIcons = document.getElementById(role + '-right-icons');
            const sendBtn = document.getElementById(role + '-send-btn');
            const leftIcon = document.getElementById(role + '-left-icon');
            
            if (input.value.trim().length > 0) {
                rightIcons.classList.add('hidden');
                sendBtn.classList.add('active');
                leftIcon.className = 'fas fa-search';
            } else {
                rightIcons.classList.remove('hidden');
                sendBtn.classList.remove('active');
                leftIcon.className = 'fas fa-camera';
            }
        }

        function toggleAttachMenu(role, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            const drawer = document.getElementById(role + '-attach-menu');
            drawer.classList.toggle('show');
        }

        function toggleStickerMenu(role, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            const drawer = document.getElementById(role + '-sticker-menu');
            drawer.classList.toggle('show');
        }

        // --- FULLY WORKING MEDIA & ATTACHMENT FUNCTIONS ---
        let activeUploadRole = null;

        function triggerFileInput(inputId, role) {
            activeUploadRole = role;
            document.getElementById(inputId).click();
            closeAllMenus();
        }

        function handleFileUpload(event, type) {
            const file = event.target.files[0];
            if(!file || !activeUploadRole) return;
            
            if(file.size > 3 * 1024 * 1024 && type !== 'image' && type !== 'camera') { 
                showToast("File is too large! Please select under 3MB.", "error");
                return;
            }

            const reader = new FileReader();
            
            if((type === 'image' || type === 'camera') && file.type.startsWith('image/')) {
                reader.readAsDataURL(file);
                reader.onload = e => {
                    const img = new Image();
                    img.src = e.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        const MAX_WIDTH = 800;
                        let scale = 1;
                        if(img.width > MAX_WIDTH) scale = MAX_WIDTH / img.width;
                        canvas.width = img.width * scale;
                        canvas.height = img.height * scale;
                        const ctx = canvas.getContext('2d');
                        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                        const compressedBase64 = canvas.toDataURL('image/jpeg', 0.6);
                        sendMediaMessage(activeUploadRole, type, compressedBase64, { fileName: file.name });
                    };
                };
            } else {
                reader.readAsDataURL(file);
                reader.onloadend = () => {
                    sendMediaMessage(activeUploadRole, type, reader.result, { fileName: file.name });
                };
            }
            event.target.value = '';
        }

        function sendContactMessage(role, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            const name = prompt("Enter Contact Name:");
            if(!name) return;
            const phone = prompt("Enter Contact Number:");
            if(!phone) return;
            sendMediaMessage(role, 'contact', null, { contactName: name, contactPhone: phone });
        }

        function sendLocationMessage(role, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            if(!navigator.geolocation) { showToast("GPS not supported.", "error"); return; }
            showToast("Acquiring Live Location...");
            navigator.geolocation.getCurrentPosition((pos) => {
                sendMediaMessage(role, 'location', null, { lat: pos.coords.latitude, lng: pos.coords.longitude });
            }, () => { showToast("Failed to get location.", "error"); }, { enableHighAccuracy: true });
        }

        function sendSticker(role, src, e) {
            if(e) e.stopPropagation();
            closeAllMenus();
            sendMediaMessage(role, 'sticker', src);
        }

        // --- FULLY WORKING VOICE RECORDER ---
        let mediaRecorder;
        let audioChunks = [];
        let recordInterval;
        let recordSeconds = 0;

        async function startRecording(role) {
            closeAllMenus();
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];
                
                document.getElementById(role + '-default-pill').style.display = 'none';
                document.getElementById(role + '-recording-pill').style.display = 'flex';
                
                recordSeconds = 0;
                document.getElementById(role + '-record-time').innerText = "00:00";
                recordInterval = setInterval(() => {
                    recordSeconds++;
                    const mins = String(Math.floor(recordSeconds / 60)).padStart(2, '0');
                    const secs = String(recordSeconds % 60).padStart(2, '0');
                    document.getElementById(role + '-record-time').innerText = `${mins}:${secs}`;
                }, 1000);

                mediaRecorder.ondataavailable = event => {
                    if (event.data.size > 0) audioChunks.push(event.data);
                };

                mediaRecorder.start();
            } catch (err) {
                showToast("Microphone access denied or not available.", "error");
            }
        }

        function cancelRecording(role) {
            if(mediaRecorder && mediaRecorder.state !== 'inactive') {
                mediaRecorder.stop();
                mediaRecorder.stream.getTracks().forEach(track => track.stop());
            }
            clearInterval(recordInterval);
            document.getElementById(role + '-recording-pill').style.display = 'none';
            document.getElementById(role + '-default-pill').style.display = 'flex';
        }

        function stopAndSendRecording(role) {
            if(mediaRecorder && mediaRecorder.state !== 'inactive') {
                mediaRecorder.onstop = () => {
                    const audioBlob = new Blob(audioChunks, { type: 'audio/webm' });
                    const reader = new FileReader();
                    reader.readAsDataURL(audioBlob);
                    reader.onloadend = () => {
                        sendMediaMessage(role, 'audio', reader.result);
                    };
                    mediaRecorder.stream.getTracks().forEach(track => track.stop());
                };
                mediaRecorder.stop();
            }
            clearInterval(recordInterval);
            document.getElementById(role + '-recording-pill').style.display = 'none';
            document.getElementById(role + '-default-pill').style.display = 'flex';
        }

        // --- GENERIC CLOUD MESSAGE SENDER WITH "DELIVERED" STATUS ---
        function sendMediaMessage(role, type, mediaUrl, extraData = {}) {
            const targetPhone = role === 'admin' ? currentAdminTargetPhone : currentClientPhone;
            if(!targetPhone) return;

            const msgData = {
                sender: role,
                type: type,
                mediaUrl: mediaUrl || '',
                time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now(),
                status: 'delivered', // Default to delivered
                readTime: '',
                ...extraData
            };

            // Check reply context
            let context = role === 'admin' ? adminReplyContext : clientReplyContext;
            if(context) {
                msgData.replyToKey = context.key;
                msgData.replyToText = context.text;
                msgData.replyToSender = context.sender;
                cancelReply(role);
            }

            db.ref(`trackings/${targetPhone}/client_messages`).push(msgData)
            .catch(e => showToast("Failed to send", "error"));
        }


        // --- LONG PRESS MODAL LOGIC ---
        let activeMsgContext = null;

        function openMessageOptions(bubbleElement, role) {
            if (navigator.vibrate) navigator.vibrate(50); // Haptic feedback
            
            const key = bubbleElement.getAttribute('data-key');
            const text = bubbleElement.getAttribute('data-text');
            const sender = bubbleElement.getAttribute('data-sender');
            const time = bubbleElement.getAttribute('data-time');
            const status = bubbleElement.getAttribute('data-status');
            const readTime = bubbleElement.getAttribute('data-readtime');
            const isMe = bubbleElement.getAttribute('data-isme') === 'true';

            activeMsgContext = { key, text, sender, role, isMe };

            document.getElementById('modal-time-sent').innerText = time;
            if(isMe) {
                document.getElementById('modal-time-read').innerHTML = status === 'read' ? `<span style="color:#53bdeb;">Read at ${readTime}</span>` : 'Delivered';
            } else {
                document.getElementById('modal-time-read').innerText = 'Received';
            }

            document.getElementById('modal-btn-delete').style.display = isMe ? 'flex' : 'none';
            document.getElementById('msg-action-overlay').classList.add('active');
        }

        function closeMsgModal() {
            document.getElementById('msg-action-overlay').classList.remove('active');
            activeMsgContext = null;
        }

        function execModalReply() {
            if(!activeMsgContext) return;
            initiateReply(activeMsgContext.key, activeMsgContext.text, activeMsgContext.sender, activeMsgContext.role, null);
            closeMsgModal();
        }
        
        function execModalCopy() {
            if(!activeMsgContext) return;
            copyChatText(activeMsgContext.text, null);
            closeMsgModal();
        }

        function execModalDelete() {
            if(!activeMsgContext) return;
            const targetPhone = activeMsgContext.role === 'admin' ? currentAdminTargetPhone : currentClientPhone;
            deleteChatMessage(targetPhone, activeMsgContext.key, null);
            closeMsgModal();
        }

        // --- SWIPE & TOUCH LOGIC FOR BUBBLES ---
        let touchStartX = 0;
        let touchCurrentX = 0;
        let swipeElement = null;
        let pressTimer = null;
        let isSwiping = false;

        function handleTouchStart(e, el, role) {
            touchStartX = e.touches[0].clientX;
            swipeElement = el;
            isSwiping = false;
            
            pressTimer = setTimeout(() => {
                if(!isSwiping) openMessageOptions(el, role);
            }, 500); // 500ms Long Press
        }

        function handleTouchMove(e) {
            if(!swipeElement) return;
            touchCurrentX = e.touches[0].clientX;
            const diffX = touchCurrentX - touchStartX;
            
            if (Math.abs(diffX) > 10) {
                isSwiping = true;
                clearTimeout(pressTimer); 
            }

            // Swipe Right to Reply
            if (diffX > 0 && diffX < 80) {
                swipeElement.style.transform = `translateX(${diffX}px)`;
                const icon = swipeElement.previousElementSibling;
                if(icon && icon.classList.contains('reply-swipe-icon')) {
                    icon.style.opacity = diffX / 80;
                    icon.style.transform = `scale(${0.5 + (diffX/160)})`;
                }
            }
        }

        function handleTouchEnd(e, el, role) {
            clearTimeout(pressTimer);
            if(!swipeElement) return;
            
            const diffX = touchCurrentX - touchStartX;
            const icon = swipeElement.previousElementSibling;
            
            if (isSwiping && diffX > 50) {
                const key = el.getAttribute('data-key');
                const text = el.getAttribute('data-text');
                const sender = el.getAttribute('data-sender');
                initiateReply(key, text, sender, role, null);
            }

            // Snap back
            swipeElement.style.transform = `translateX(0px)`;
            if(icon && icon.classList.contains('reply-swipe-icon')) {
                icon.style.opacity = 0;
                icon.style.transform = `scale(0.5)`;
            }
            
            swipeElement = null;
            isSwiping = false;
        }

        // Desktop fallback for long press
        function handleMouseDown(e, el, role) {
            pressTimer = setTimeout(() => openMessageOptions(el, role), 600);
        }
        function handleMouseUp(e) { clearTimeout(pressTimer); }
        function handleMouseLeave(e) { clearTimeout(pressTimer); }


        // --- 1. PREMIUM AUDIO & PUSH NOTIFICATIONS ---
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js').then(function(registration) {
                console.log('SW Registered');
            }).catch(function(error) {});
        }

        function requestNotificationPermission() {
            if ("Notification" in window && Notification.permission !== "granted" && Notification.permission !== "denied") {
                Notification.requestPermission();
            }
        }

        function playPremiumChime() {
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = ctx.createOscillator(); const gain = ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(880, ctx.currentTime); 
                osc.frequency.exponentialRampToValueAtTime(440, ctx.currentTime + 0.5); 
                gain.gain.setValueAtTime(0.4, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 1.5);
                osc.connect(gain); gain.connect(ctx.destination);
                osc.start(); osc.stop(ctx.currentTime + 1.5);
            } catch(e) {}
        }

        function showPushNotification(title, body) {
            playPremiumChime();
            
            if ("Notification" in window && Notification.permission === "granted" && 'serviceWorker' in navigator) {
                navigator.serviceWorker.ready.then(function(registration) {
                    registration.showNotification(title, {
                        body: body, icon: "https://cdn-icons-png.flaticon.com/512/814/814513.png", vibrate: [200, 100, 200], tag: "mnd-logistics", renotify: true
                    });
                });
            } else if ("Notification" in window && Notification.permission === "granted") {
                new Notification(title, { body: body, icon: "https://cdn-icons-png.flaticon.com/512/814/814513.png" });
            }

            const container = document.getElementById('toast-container');
            const push = document.createElement('div');
            push.className = 'native-push';
            push.innerHTML = `
                <div class="native-push-icon"><i class="fas fa-bell"></i></div>
                <div class="native-push-content">
                    <div class="native-push-title">${escapeHTML(title)} <span>Just Now</span></div>
                    <p class="native-push-body">${escapeHTML(body)}</p>
                </div>
            `;
            container.prepend(push); 
            setTimeout(() => { push.remove(); }, 6500);
        }

        function showToast(message, type = 'success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            const icon = type === 'success' ? 'fa-check-circle' : 'fa-exclamation-triangle';
            toast.innerHTML = `<i class="fas ${icon}"></i> <span>${escapeHTML(message)}</span>`;
            container.appendChild(toast);
            setTimeout(() => { toast.remove(); }, 4000);
        }

        // --- 2. FIREBASE CONFIGURATION ---
        const firebaseConfig = {
            apiKey: "AIzaSyCZP-zuJNDW9S4sD_d4R_-nrTMjf0HD4MM",
            authDomain: "mnd-tracking.firebaseapp.com",
            databaseURL: "https://mnd-tracking-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-tracking",
            storageBucket: "mnd-tracking.firebasestorage.app",
            messagingSenderId: "794366177184",
            appId: "1:794366177184:web:3f394f0207215ccca0fec5"
        };
        
        if (!firebase.apps.length) { firebase.initializeApp(firebaseConfig); }
        const db = firebase.database();

        // --- 3. SYSTEM STATE & CONFIG ---
        const ADMIN_NUMBERS = ["9771617808", "9153635378", "7294969938", "+918544341240", "8544341240"];
        const MASTER_PIN = "121120";
        
        let geoWatchId = null; 
        let activeClientData = null; 
        let pingCount = 0;
        
        let currentClientPhone = ""; 
        let currentClientName = "Client";
        let currentAdminTargetPhone = ""; 

        // Memory for Push Notifications
        let processedUpdateKeys = new Set();
        let processedChatKeys = new Set();
        let isInitialLoad = true;
        let isChatInitialLoad = true;

        // --- 4. VIEW ROUTER ---
        function switchView(viewId) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(viewId).classList.add('active-view');
            document.querySelectorAll('.error-msg').forEach(el => el.style.display = 'none');
            window.scrollTo(0,0);
        }

        // --- 5. THE GATEKEEPER LOGIC ---
        function processLogin(e) {
            if(e) e.stopPropagation();
            requestNotificationPermission();

            const phone = document.getElementById('login-phone').value.trim();
            const pin = document.getElementById('login-pin').value.trim();
            const err = document.getElementById('login-error');
            const btn = document.getElementById('login-btn');

            if(!phone || !pin) { err.innerHTML = "<i class='fas fa-exclamation'></i> Credentials required."; err.style.display = 'block'; return; }

            // Admin Logic
            if(ADMIN_NUMBERS.includes(phone) && pin === MASTER_PIN) {
                document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                switchView('view-admin');
                startAdminHistoryListener(); 
                showToast("Admin Protocol Authorized");
                return;
            }

            // Client Logic
            btn.innerHTML = '<i class="fas fa-circle-notch fa-spin"></i> DECRYPTING...';
            btn.disabled = true; err.style.display = 'none';

            db.ref('trackings/' + phone).once('value').then((snapshot) => {
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE';
                btn.disabled = false;

                if (snapshot.exists()) {
                    const clientData = snapshot.val();
                    if(clientData.pin === pin || !clientData.pin) {
                        document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
                        document.getElementById('client-dash-name').innerText = escapeHTML(clientData.name) || "Client";
                        document.getElementById('client-dash-event').innerText = escapeHTML(clientData.event) || "Event Logistics";
                        
                        currentClientPhone = phone;
                        currentClientName = escapeHTML(clientData.name) || "Client";
                        
                        processedUpdateKeys.clear(); processedChatKeys.clear();
                        isInitialLoad = true; isChatInitialLoad = true;

                        startClientWatchListeners(); 
                        switchView('view-client');
                        showToast(`Welcome, ${currentClientName}`);
                    } else {
                        err.innerHTML = "<i class='fas fa-shield-alt'></i> Incorrect Security PIN."; err.style.display = 'block';
                    }
                } else {
                    err.innerHTML = "<i class='fas fa-search'></i> Profile not found in Active Database."; err.style.display = 'block';
                }
            }).catch((error) => {
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> INITIATE HANDSHAKE'; btn.disabled = false;
                err.innerHTML = "<i class='fas fa-wifi'></i> Server connection failed. Check Database Rules."; err.style.display = 'block';
            });
        }

        // --- 6. ADMIN: PROVISION & UPDATE PIN ---
        function adminGeneratePin(e) {
            if(e) e.stopPropagation();
            const name = document.getElementById('admin-c-name').value.trim();
            const phone = document.getElementById('admin-c-phone').value.trim();
            const event = document.getElementById('admin-c-event').value.trim();
            const customPinInput = document.getElementById('admin-c-custom-pin').value.trim();
            const btn = document.getElementById('btn-generate');

            if(!name || !phone || !event) { showToast("Name, Phone, and Event required.", "error"); return; }
            if(customPinInput && customPinInput.length !== 6) { showToast("Custom PIN must be exactly 6 digits.", "error"); return; }

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SAVING TO CLOUD...'; btn.disabled = true;

            const finalPin = customPinInput ? customPinInput : Math.floor(100000 + Math.random() * 900000).toString();
            const timestamp = Date.now();
            const humanDate = new Date().toLocaleString('en-US', { day:'numeric', month:'short', hour:'2-digit', minute:'2-digit'});

            activeClientData = { name, phone, event, pin: finalPin, timestamp: timestamp, dateStr: humanDate };

            db.ref('trackings/' + phone).update(activeClientData).then(() => {
                document.getElementById('display-new-pin').innerText = finalPin;
                document.getElementById('admin-pin-result').style.display = 'block';
                
                document.getElementById('admin-c-name').value = ''; document.getElementById('admin-c-phone').value = '';
                document.getElementById('admin-c-event').value = ''; document.getElementById('admin-c-custom-pin').value = '';

                btn.innerHTML = '<i class="fas fa-microchip"></i> AUTHORIZE & SAVE TO CLOUD'; btn.disabled = false;
                showToast("Client Provisioned/Updated Successfully");
            }).catch((error) => {
                showToast("Database Permission Denied. Check Rules.", "error");
                btn.innerHTML = '<i class="fas fa-microchip"></i> AUTHORIZE & SAVE TO CLOUD'; btn.disabled = false;
            });
        }

        function adminShareWhatsApp(e) {
            if(e) e.stopPropagation();
            if (!activeClientData) return;
            
            const msg = `*👑 MAA NIRMALA DJ & TENT HOUSE BELTIKRI ( 813106 ) 👑*\n\n *📍 LIVE LOCATION TRACKING📍*\n\nHello ${activeClientData.name},\nYour logistics for *${activeClientData.event}* are active.\n----------------------------------------------------------\n*How to track:*\n1. Click the link and open the website.\n2. At the bottom right corner (above the MND AI button), click the 'Live Location' button to open the tracking portal.\n3. Enter your registered mobile number and 6-digit PIN to track.\n----------------------------------------------------------\n •••••••••••••••••••••••••••••••••••••••••••••••\n 🔗 *Link:* https://maa-nirmala-dj.github.io/-tent-house./\n📱 *Login Number:* ${activeClientData.phone}\n🔐 *Tracking PIN:* ${activeClientData.pin}\n•••••••••••••••••••••••••••••••••••••••••••••••\nThank You.`;

            const cleanPhone = activeClientData.phone.replace(/\D/g, '');
            window.open(`https://wa.me/91${cleanPhone}?text=${encodeURIComponent(msg)}`, '_blank');
        }

        // --- 7. ADMIN: SEQUENTIAL HISTORY LIST ---
        function startAdminHistoryListener() {
            const listArea = document.getElementById('admin-active-list');
            db.ref('trackings').orderByChild('timestamp').on('value', (snapshot) => {
                if(snapshot.exists()) {
                    let html = '';
                    const records = [];
                    snapshot.forEach(child => {
                        let data = child.val();
                        if(data.name && data.phone) { records.push(data); }
                    });
                    
                    records.reverse().forEach(data => {
                        const safeName = escapeHTML(data.name);
                        const safeEvent = escapeHTML(data.event || 'Logistics Event');
                        const jsSafeName = data.name.replace(/'/g, "\\'");
                        const jsSafeEvent = (data.event || 'Logistics Event').replace(/'/g, "\\'");
                        
                        html += `
                            <div class="client-list-item" onclick="openAdminSession('${data.phone}', '${jsSafeName}', '${jsSafeEvent}')">
                                <div style="width: 60%; overflow: hidden;">
                                    <h4 style="white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">${safeName}</h4>
                                    <p style="color:#D4AF37; margin-bottom:2px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">${safeEvent}</p>
                                    <p>${data.phone} • <span style="font-size:9px;">${data.dateStr || 'Legacy Record'}</span></p>
                                </div>
                                <div class="client-list-actions">
                                    <div class="client-list-pin" title="Active PIN">${data.pin || 'N/A'}</div>
                                    <button class="action-icon-btn trash" onclick="event.stopPropagation(); revokeAccess('${data.phone}')" title="Revoke"><i class="fas fa-trash-alt"></i></button>
                                </div>
                            </div>
                        `;
                    });
                    listArea.innerHTML = html;
                } else {
                    listArea.innerHTML = '<div style="text-align:center; color:#555; font-size:13px; padding:20px;">History is empty.</div>';
                }
            });
        }

        function revokeAccess(phone) {
            if(confirm("Are you sure you want to permanently revoke tracking access for this number?")) {
                db.ref('trackings/' + phone).remove().then(() => showToast("Access Revoked")).catch(err => showToast("Error", "error"));
            }
        }

        function openAdminSession(phone, name, eventName) {
            currentAdminTargetPhone = phone;
            document.getElementById('session-c-name').innerText = escapeHTML(name);
            document.getElementById('session-c-event').innerText = escapeHTML(eventName) || "Event Logistics";
            document.getElementById('session-c-phone').innerText = phone;
            document.getElementById('admin-chat-header-name').innerText = escapeHTML(name); 
            
            switchView('view-admin-session');
            showToast(`Target Locked: ${name}`, 'success');

            processedChatKeys.clear();
            isChatInitialLoad = true;
            cancelReply('admin');

            db.ref(`trackings/${currentAdminTargetPhone}/client_location`).on('value', (snap) => {
                const card = document.getElementById('admin-client-loc-card');
                if(snap.exists()) {
                    card.style.display = 'block';
                    const loc = snap.val();
                    document.getElementById('admin-view-client-map').href = `https://maps.google.com/?q=${loc.lat},${loc.lng}`;
                } else {
                    card.style.display = 'none';
                }
            });

            db.ref(`trackings/${currentAdminTargetPhone}/updates`).orderByChild('timestamp').on('value', (snap) => {
                const area = document.getElementById('admin-manage-updates-area');
                if(snap.exists()) {
                    let html = '';
                    const updates = [];
                    snap.forEach(child => updates.push({ key: child.key, ...child.val() }));
                    
                    updates.reverse().forEach(u => {
                        html += `
                            <div class="timeline-item" style="padding-bottom:10px; margin-bottom:10px;">
                                <div class="update-content">
                                    <div class="update-time">${escapeHTML(u.time)}</div>
                                    <div class="update-text">${escapeHTML(u.text)}</div>
                                </div>
                                <button class="action-icon-btn trash" onclick="adminDeleteUpdate('${u.key}', event)"><i class="fas fa-times-circle"></i></button>
                            </div>
                        `;
                    });
                    area.innerHTML = html;
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:#555; text-align:center; margin-top:10px;">No updates sent yet.</div>';
                }
            });

            // 3. 💬 ADVANCED TWO-WAY CHAT LISTENER (ADMIN VIEW)
            db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).orderByChild('timestamp').limitToLast(100).on('value', (snap) => {
                const area = document.getElementById('admin-chat-area');
                if(snap.exists()) {
                    let html = '';
                    let updatesToRead = {};
                    
                    snap.forEach(child => {
                        const key = child.key;
                        const m = child.val();

                        // Mark incoming messages as read
                        if(m.sender === 'client' && m.status !== 'read') {
                            updatesToRead[`${key}/status`] = 'read';
                            updatesToRead[`${key}/readTime`] = new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'});
                        }
                        
                        if (!isChatInitialLoad && !processedChatKeys.has(key) && m.sender === 'client') {
                            let notifBody = m.type ? `Sent an attachment 📎` : m.text;
                            showPushNotification(`Message from ${name}`, notifBody);
                        }
                        processedChatKeys.add(key);

                        const isMe = m.sender === 'admin';
                        const bubbleClass = isMe ? 'sent' : 'received'; 
                        
                        const safeTextUI = escapeHTML(m.text || '');
                        const safeTextJS = (m.text||'').replace(/'/g, "\\'").replace(/"/g, "&quot;");
                        
                        let replyHtml = '';
                        if(m.replyToText) {
                            const senderName = m.replyToSender === 'admin' ? 'HQ Dispatch' : escapeHTML(name);
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${senderName}</div>${escapeHTML(m.replyToText)}</div>`;
                        }

                        let messageContentHtml = '';
                        if (m.type === 'image' || m.type === 'camera') {
                            messageContentHtml = `<img src="${escapeHTML(m.mediaUrl)}" style="max-width: 100%; border-radius: 8px; cursor: pointer; display:block;" onclick="window.open('${escapeHTML(m.mediaUrl)}', '_blank')">`;
                        } else if (m.type === 'audio') {
                            messageContentHtml = `<audio controls src="${escapeHTML(m.mediaUrl)}" style="max-width: 220px; height: 35px; outline: none; margin:5px 0;"></audio>`;
                        } else if (m.type === 'document') {
                            messageContentHtml = `<a href="${escapeHTML(m.mediaUrl)}" download="${escapeHTML(m.fileName || 'document')}" style="display:flex; align-items:center; gap:10px; background:rgba(0,0,0,0.3); padding:12px; border-radius:8px; color:var(--neon-cyan); text-decoration:none; margin:5px 0;"><i class="fas fa-file-alt" style="font-size:24px;"></i> <div><div style="font-weight:bold; font-size:13px;">${escapeHTML(m.fileName || 'Document')}</div><div style="font-size:10px; color:#aaa;">Click to download</div></div></a>`;
                        } else if (m.type === 'contact') {
                            messageContentHtml = `<div style="display:flex; align-items:center; gap:12px; background:rgba(0,0,0,0.3); padding:10px; border-radius:8px; margin:5px 0; min-width: 150px;"><div style="width:40px; height:40px; min-width:40px; border-radius:50%; background:#3b82f6; display:flex; justify-content:center; align-items:center; color:#fff; font-size:18px;"><i class="fas fa-user"></i></div><div><div style="font-weight:bold; font-size:14px; color:#fff;">${escapeHTML(m.contactName)}</div><a href="tel:${escapeHTML(m.contactPhone)}" style="color:var(--neon-cyan); font-size:12px; text-decoration:none;">${escapeHTML(m.contactPhone)}</a></div></div>`;
                        } else if (m.type === 'location') {
                            messageContentHtml = `<a href="https://maps.google.com/?q=${m.lat},${m.lng}" target="_blank" style="display:block; text-decoration:none; color:inherit; margin:5px 0;"><div style="background:rgba(0,0,0,0.3); border-radius:8px; overflow:hidden;"><img src="https://via.placeholder.com/400x200/202c33/00E5FF?text=📍+Live+Location+Pin" alt="Location" style="width:100%; height:120px; object-fit:cover;"><div style="padding:10px;"><div style="color:var(--neon-cyan); font-weight:bold; font-size:13px;"><i class="fas fa-map-marker-alt"></i> Pinned Location</div><div style="font-size:11px; color:#aaa; margin-top:3px;">Tap to view in Maps</div></div></div></a>`;
                        } else if (m.type === 'sticker') {
                            messageContentHtml = `<img src="${escapeHTML(m.mediaUrl)}" style="width: 120px; height: 120px; background: transparent; display:block;">`;
                        } else {
                            messageContentHtml = `<div>${safeTextUI}</div>`; // Standard Text
                        }

                        let statusHtml = '';
                        if(isMe) {
                            if(m.status === 'read') statusHtml = '<span class="msg-status read"><i class="fas fa-check-double"></i></span>';
                            else if(m.status === 'delivered') statusHtml = '<span class="msg-status"><i class="fas fa-check-double"></i></span>';
                            else statusHtml = '<span class="msg-status"><i class="fas fa-check"></i></span>';
                        }

                        html += `
                            <div class="bubble-wrapper">
                                <div class="reply-swipe-icon"><i class="fas fa-reply"></i></div>
                                <div class="chat-bubble ${bubbleClass}" id="msg-${key}"
                                    data-key="${key}" data-text="${safeTextJS}" data-sender="${m.sender}" 
                                    data-time="${escapeHTML(m.time)}" data-readtime="${escapeHTML(m.readTime||'')}" 
                                    data-status="${m.status}" data-isme="${isMe}"
                                    onmousedown="handleMouseDown(event, this, 'admin')" onmouseup="handleMouseUp(event)" onmouseleave="handleMouseLeave(event)"
                                    ontouchstart="handleTouchStart(event, this, 'admin')" ontouchmove="handleTouchMove(event)" ontouchend="handleTouchEnd(event, this, 'admin')">
                                    ${replyHtml}
                                    ${messageContentHtml}
                                    <div class="chat-time">${escapeHTML(m.time)} ${statusHtml}</div>
                                </div>
                            </div>
                        `;
                    });
                    
                    if(Object.keys(updatesToRead).length > 0) {
                        db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).update(updatesToRead);
                    }

                    isChatInitialLoad = false;
                    area.innerHTML = html;
                    area.scrollTop = area.scrollHeight; // Auto-scroll
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>';
                    isChatInitialLoad = false;
                }
            });
        }

        function closeAdminSession(e) {
            if(e) e.stopPropagation();
            if(geoWatchId !== null) toggleLocationBroadcast(); 
            if(currentAdminTargetPhone) {
                db.ref(`trackings/${currentAdminTargetPhone}/client_location`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/updates`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).off();
            }
            currentAdminTargetPhone = "";
            switchView('view-admin');
        }

        // --- 8. AI REVERSE GEOCODING (Nearest Village) ---
        async function fetchLocationName(lat, lng) {
            try {
                const res = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lng}&zoom=14`);
                const data = await res.json();
                if(data && data.address) {
                    return data.address.village || data.address.town || data.address.city || data.address.county || data.display_name.split(',')[0];
                }
                return "Unknown Zone";
            } catch(e) { return "Coordinates Locked"; }
        }

        // --- 9. ADMIN: TARGETED GPS TELEMETRY ---
        function toggleLocationBroadcast(e) {
            if(e) e.stopPropagation();
            const btn = document.getElementById('btn-broadcast');
            const metrics = document.getElementById('admin-telemetrics');
            const locDisp = document.getElementById('admin-ai-location');

            if(!currentAdminTargetPhone) return;

            if(geoWatchId !== null) {
                navigator.geolocation.clearWatch(geoWatchId);
                geoWatchId = null; pingCount = 0;
                btn.innerHTML = '<i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET';
                btn.style.background = "transparent"; btn.style.color = "var(--neon-cyan)"; btn.style.borderColor = "var(--neon-cyan)";
                metrics.style.display = 'none'; locDisp.style.display = 'none';
                
                db.ref(`trackings/${currentAdminTargetPhone}/location/status`).set('offline').catch(err => console.warn(err)); 
                showToast("Transmission Terminated", "error");
            } else {
                if(!navigator.geolocation) { showToast("Hardware GPS not supported.", "error"); return; }
                btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> ACQUIRING SATELLITES...';
                
                geoWatchId = navigator.geolocation.watchPosition(
                    async (pos) => {
                        btn.innerHTML = '<i class="fas fa-times-circle"></i> STOP BROADCASTING';
                        btn.style.background = "rgba(255,51,51,0.1)"; btn.style.color = "var(--danger)"; btn.style.borderColor = "var(--danger)";
                        metrics.style.display = 'grid'; locDisp.style.display = 'flex';
                        
                        pingCount++;
                        const speedKmh = pos.coords.speed ? (pos.coords.speed * 3.6).toFixed(1) : 0;
                        
                        document.getElementById('a-speed').innerText = speedKmh + " km/h";
                        document.getElementById('a-acc').innerText = "±" + Math.round(pos.coords.accuracy) + "m";
                        document.getElementById('a-ping').innerText = pingCount;

                        let locName = "Resolving...";
                        if(pingCount % 10 === 1) { 
                            locName = await fetchLocationName(pos.coords.latitude, pos.coords.longitude);
                            document.getElementById('a-loc-name').innerText = escapeHTML(locName);
                        } else {
                            locName = document.getElementById('a-loc-name').innerText;
                        }

                        db.ref(`trackings/${currentAdminTargetPhone}/location`).set({
                            status: 'online', lat: pos.coords.latitude, lng: pos.coords.longitude,
                            speed: speedKmh, heading: pos.coords.heading ? Math.round(pos.coords.heading) + "°" : "N/A",
                            accuracy: Math.round(pos.coords.accuracy), time: new Date().toLocaleTimeString(),
                            locationName: locName
                        });
                        
                        if(pingCount === 1) showToast("Satellite Uplink Established");
                    },
                    (err) => {
                        showToast("GPS Error. Ensure Location Services are ON.", "error");
                        if(geoWatchId !== null) navigator.geolocation.clearWatch(geoWatchId);
                        geoWatchId = null; btn.innerHTML = '<i class="fas fa-location-arrow"></i> INITIATE BROADCAST TO TARGET';
                    }, 
                    { enableHighAccuracy: true, maximumAge: 0, timeout: 10000 }
                );
            }
        }

        // --- 10. ADMIN: TARGETED OFFICIAL UPDATE (Timeline) ---
        function setQuickUpdate(msg, e) { 
            if(e) e.stopPropagation();
            document.getElementById('admin-update-text').value = msg; 
        }

        function adminPushUpdate(e) {
            if(e) e.stopPropagation();
            if(!currentAdminTargetPhone) return;

            const text = document.getElementById('admin-update-text').value.trim();
            const btn = document.getElementById('btn-push-update');
            if(!text) { showToast("Enter an official update to transmit.", "error"); return; }

            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> TRANSMITTING...'; btn.disabled = true;

            db.ref(`trackings/${currentAdminTargetPhone}/updates`).push({
                text: text, time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}), timestamp: Date.now()
            }).then(() => {
                document.getElementById('admin-update-text').value = '';
                btn.innerHTML = '<i class="fas fa-paper-plane"></i> TRANSMIT TO TIMELINE'; btn.disabled = false;
                showToast("Official Update Transmitted");
            }).catch(err => {
                showToast("Permission Denied", "error"); btn.disabled = false;
            });
        }

        function adminDeleteUpdate(key, e) {
            if(e) e.stopPropagation();
            if(!currentAdminTargetPhone) return;
            db.ref(`trackings/${currentAdminTargetPhone}/updates/${key}`).remove()
            .then(() => showToast("Update Deleted"))
            .catch(e => showToast("Failed to delete", "error"));
        }

        // --- 11. 💬 WHATSAPP-STYLE CHAT FUNCTIONS (TWO-WAY) ---
        function copyChatText(text, e) {
            if(e) e.stopPropagation();
            const rawText = text.replace(/&quot;/g, '"').replace(/\\'/g, "'");
            navigator.clipboard.writeText(rawText).then(() => {
                showToast("Copied to clipboard!");
            }).catch(err => {
                showToast("Failed to copy", "error");
            });
        }

        function initiateReply(key, text, originalSenderRole, role, e) {
            if(e) e.stopPropagation();
            
            let rawText = text.replace(/&quot;/g, '"').replace(/\\'/g, "'");
            let shortText = rawText.length > 40 ? rawText.substring(0, 40) + '...' : rawText;
            
            if(role === 'admin') {
                const senderName = originalSenderRole === 'admin' ? 'HQ Dispatch' : document.getElementById('session-c-name').innerText;
                adminReplyContext = { key, text: shortText, sender: originalSenderRole };
                document.getElementById('admin-reply-sender').innerText = senderName;
                document.getElementById('admin-reply-text').innerText = escapeHTML(shortText);
                document.getElementById('admin-reply-banner').style.display = 'flex';
                document.getElementById('admin-chat-input').focus();
            } else {
                const senderName = originalSenderRole === 'admin' ? 'HQ Dispatch' : currentClientName;
                clientReplyContext = { key, text: shortText, sender: originalSenderRole };
                document.getElementById('client-reply-sender').innerText = senderName;
                document.getElementById('client-reply-text').innerText = escapeHTML(shortText);
                document.getElementById('client-reply-banner').style.display = 'flex';
                document.getElementById('client-chat-input').focus();
            }
        }

        function cancelReply(role, e) {
            if(e) e.stopPropagation();
            if(role === 'admin') {
                adminReplyContext = null;
                document.getElementById('admin-reply-banner').style.display = 'none';
            } else {
                clientReplyContext = null;
                document.getElementById('client-reply-banner').style.display = 'none';
            }
        }

        function adminSendChatMessage(e) {
            if(e) e.stopPropagation();
            if(!currentAdminTargetPhone) return;
            
            const input = document.getElementById('admin-chat-input');
            const text = input.value.trim();
            if(!text) return;

            const msgData = {
                text: text, sender: 'admin',
                time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now(),
                status: 'delivered',
                readTime: ''
            };

            if(adminReplyContext) {
                msgData.replyToKey = adminReplyContext.key;
                msgData.replyToText = adminReplyContext.text;
                msgData.replyToSender = adminReplyContext.sender;
            }

            db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).push(msgData).then(() => {
                input.value = '';
                handleInputTyping('admin'); // Reset UI
                cancelReply('admin');
            }).catch(e => showToast("Send failed", "error"));
        }

        function clientSendChatMessage(e) {
            if(e) e.stopPropagation();
            if(!currentClientPhone) return;
            
            const input = document.getElementById('client-chat-input');
            const text = input.value.trim();
            if(!text) return;

            const msgData = {
                text: text, sender: 'client',
                time: new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'}),
                timestamp: Date.now(),
                status: 'delivered',
                readTime: ''
            };

            if(clientReplyContext) {
                msgData.replyToKey = clientReplyContext.key;
                msgData.replyToText = clientReplyContext.text;
                msgData.replyToSender = clientReplyContext.sender;
            }

            db.ref(`trackings/${currentClientPhone}/client_messages`).push(msgData).then(() => {
                input.value = '';
                handleInputTyping('client'); // Reset UI
                cancelReply('client');
            }).catch(e => showToast("Send failed", "error"));
        }

        function deleteChatMessage(targetPhone, key, e) {
            if(e) e.stopPropagation();
            if(!targetPhone || !key) return;
            
            db.ref(`trackings/${targetPhone}/client_messages/${key}`).remove()
            .catch(err => showToast("Failed to delete", "error"));
        }

        // --- 12. CLIENT: SHARE LOCATION TO HQ ---
        function clientShareLocation(e) {
            if(e) e.stopPropagation();
            if(!currentClientPhone) return;
            if(!navigator.geolocation) { showToast("GPS not supported on this device.", "error"); return; }
            
            showToast("Acquiring your location...");
            navigator.geolocation.getCurrentPosition((pos) => {
                db.ref(`trackings/${currentClientPhone}/client_location`).set({
                    lat: pos.coords.latitude, lng: pos.coords.longitude, time: Date.now()
                }).then(() => {
                    showToast("Location Shared with HQ Successfully!");
                });
            }, (err) => {
                showToast("Failed to get location. Ensure GPS is ON.", "error");
            }, { enableHighAccuracy: true });
        }

        // --- 13. CLIENT: TARGETED WATCH LISTENERS & PUSH NOTIFICATIONS ---
        function startClientWatchListeners() {
            if(!currentClientPhone) return;

            db.ref(`trackings/${currentClientPhone}/location`).on('value', (snapshot) => {
                const badge = document.getElementById('client-conn-status');
                if(snapshot.exists() && snapshot.val().status === 'online') {
                    const data = snapshot.val();
                    badge.innerHTML = '<i class="fas fa-circle fa-beat"></i> ONLINE'; badge.className = 'status-badge';
                    document.getElementById('client-gps-time').innerHTML = `<span style="color:var(--neon-green);">Signal Locked (${escapeHTML(data.time)})</span>`;
                    document.getElementById('c-speed').innerText = data.speed + " km/h";
                    document.getElementById('c-heading').innerText = data.heading;
                    document.getElementById('c-accuracy').innerText = "±" + data.accuracy + "m";
                    
                    document.getElementById('map-loader-text').style.display = 'none';
                    document.getElementById('ext-map-link').style.display = 'block';
                    document.getElementById('ext-map-link').href = `https://maps.google.com/?q=${data.lat},${data.lng}`;

                    const locDisp = document.getElementById('client-location-display');
                    if(data.locationName && data.locationName !== "Resolving...") {
                        locDisp.style.display = 'flex';
                        document.getElementById('c-location-name').innerText = escapeHTML(data.locationName);
                    }

                    if(Math.abs(data.lat - lastKnownLat) > 0.0001 || Math.abs(data.lng - lastKnownLng) > 0.0001) {
                        const mapUrl = `https://maps.google.com/maps?q=${data.lat},${data.lng}&z=15&output=embed`;
                        document.getElementById('client-map-iframe').src = mapUrl;
                        lastKnownLat = data.lat; lastKnownLng = data.lng;
                    }
                } else {
                    badge.innerHTML = '<i class="fas fa-exclamation-circle"></i> SIGNAL LOST'; badge.className = 'status-badge offline';
                    document.getElementById('client-gps-time').innerText = "Target Offline";
                }
            });

            db.ref(`trackings/${currentClientPhone}/updates`).orderByChild('timestamp').limitToLast(50).on('value', (snapshot) => {
                const feedArea = document.getElementById('client-updates-area');
                if(snapshot.exists()) {
                    let html = '';
                    const updates = [];
                    
                    snapshot.forEach(child => {
                        const key = child.key;
                        const updateData = child.val();
                        updates.push({key, ...updateData});

                        if (!isInitialLoad && !processedUpdateKeys.has(key)) {
                            showPushNotification("Official Logistics Update", updateData.text);
                        }
                        processedUpdateKeys.add(key);
                    });
                    
                    isInitialLoad = false; 
                    
                    updates.reverse().forEach((update) => {
                        html += `
                            <div class="timeline-item">
                                <div class="update-content">
                                    <div class="update-time">${escapeHTML(update.time)}</div>
                                    <div class="update-text">${escapeHTML(update.text)}</div>
                                </div>
                            </div>
                        `;
                    });
                    feedArea.innerHTML = html;
                } else {
                    feedArea.innerHTML = '<div style="text-align:center; color:#555; font-size:13px; padding:20px; border:none; margin-left:-20px;">Awaiting communications...</div>';
                    isInitialLoad = false; 
                }
            });

            db.ref(`trackings/${currentClientPhone}/client_messages`).orderByChild('timestamp').limitToLast(100).on('value', (snap) => {
                const area = document.getElementById('client-chat-area');
                if(snap.exists()) {
                    let html = '';
                    let updatesToRead = {};
                    
                    snap.forEach(child => {
                        const key = child.key;
                        const m = child.val();
                        
                        // Mark incoming admin messages as read automatically
                        if(m.sender === 'admin' && m.status !== 'read') {
                            updatesToRead[`${key}/status`] = 'read';
                            updatesToRead[`${key}/readTime`] = new Date().toLocaleTimeString('en-US', {hour: '2-digit', minute:'2-digit'});
                        }

                        if (!isChatInitialLoad && !processedChatKeys.has(key) && m.sender === 'admin') {
                            let notifBody = m.type ? `Sent an attachment 📎` : m.text;
                            showPushNotification("New Message from HQ", notifBody);
                        }
                        processedChatKeys.add(key);

                        const isMe = m.sender === 'client';
                        const bubbleClass = isMe ? 'sent' : 'received';
                        
                        const safeTextUI = escapeHTML(m.text || '');
                        const safeTextJS = (m.text||'').replace(/'/g, "\\'").replace(/"/g, "&quot;");
                        
                        let replyHtml = '';
                        if(m.replyToText) {
                            const senderName = m.replyToSender === 'client' ? 'You' : 'HQ Dispatch';
                            replyHtml = `<div class="chat-reply-context"><div class="sender">${senderName}</div>${escapeHTML(m.replyToText)}</div>`;
                        }

                        let messageContentHtml = '';
                        if (m.type === 'image' || m.type === 'camera') {
                            messageContentHtml = `<img src="${escapeHTML(m.mediaUrl)}" style="max-width: 100%; border-radius: 8px; cursor: pointer; display:block;" onclick="window.open('${escapeHTML(m.mediaUrl)}', '_blank')">`;
                        } else if (m.type === 'audio') {
                            messageContentHtml = `<audio controls src="${escapeHTML(m.mediaUrl)}" style="max-width: 220px; height: 35px; outline: none; margin:5px 0;"></audio>`;
                        } else if (m.type === 'document') {
                            messageContentHtml = `<a href="${escapeHTML(m.mediaUrl)}" download="${escapeHTML(m.fileName || 'document')}" style="display:flex; align-items:center; gap:10px; background:rgba(0,0,0,0.3); padding:12px; border-radius:8px; color:var(--neon-cyan); text-decoration:none; margin:5px 0;"><i class="fas fa-file-alt" style="font-size:24px;"></i> <div><div style="font-weight:bold; font-size:13px;">${escapeHTML(m.fileName || 'Document')}</div><div style="font-size:10px; color:#aaa;">Click to download</div></div></a>`;
                        } else if (m.type === 'contact') {
                            messageContentHtml = `<div style="display:flex; align-items:center; gap:12px; background:rgba(0,0,0,0.3); padding:10px; border-radius:8px; margin:5px 0; min-width: 150px;"><div style="width:40px; height:40px; min-width:40px; border-radius:50%; background:#3b82f6; display:flex; justify-content:center; align-items:center; color:#fff; font-size:18px;"><i class="fas fa-user"></i></div><div><div style="font-weight:bold; font-size:14px; color:#fff;">${escapeHTML(m.contactName)}</div><a href="tel:${escapeHTML(m.contactPhone)}" style="color:var(--neon-cyan); font-size:12px; text-decoration:none;">${escapeHTML(m.contactPhone)}</a></div></div>`;
                        } else if (m.type === 'location') {
                            messageContentHtml = `<a href="https://maps.google.com/?q=${m.lat},${m.lng}" target="_blank" style="display:block; text-decoration:none; color:inherit; margin:5px 0;"><div style="background:rgba(0,0,0,0.3); border-radius:8px; overflow:hidden;"><img src="https://via.placeholder.com/400x200/202c33/00E5FF?text=📍+Live+Location+Pin" alt="Location" style="width:100%; height:120px; object-fit:cover;"><div style="padding:10px;"><div style="color:var(--neon-cyan); font-weight:bold; font-size:13px;"><i class="fas fa-map-marker-alt"></i> Pinned Location</div><div style="font-size:11px; color:#aaa; margin-top:3px;">Tap to view in Maps</div></div></div></a>`;
                        } else if (m.type === 'sticker') {
                            messageContentHtml = `<img src="${escapeHTML(m.mediaUrl)}" style="width: 120px; height: 120px; background: transparent; display:block;">`;
                        } else {
                            messageContentHtml = `<div>${safeTextUI}</div>`; // Standard Text
                        }

                        let statusHtml = '';
                        if(isMe) {
                            if(m.status === 'read') statusHtml = '<span class="msg-status read"><i class="fas fa-check-double"></i></span>';
                            else if(m.status === 'delivered') statusHtml = '<span class="msg-status"><i class="fas fa-check-double"></i></span>';
                            else statusHtml = '<span class="msg-status"><i class="fas fa-check"></i></span>';
                        }

                        html += `
                            <div class="bubble-wrapper">
                                <div class="reply-swipe-icon"><i class="fas fa-reply"></i></div>
                                <div class="chat-bubble ${bubbleClass}" id="cmsg-${key}"
                                    data-key="${key}" data-text="${safeTextJS}" data-sender="${m.sender}" 
                                    data-time="${escapeHTML(m.time)}" data-readtime="${escapeHTML(m.readTime||'')}" 
                                    data-status="${m.status}" data-isme="${isMe}"
                                    onmousedown="handleMouseDown(event, this, 'client')" onmouseup="handleMouseUp(event)" onmouseleave="handleMouseLeave(event)"
                                    ontouchstart="handleTouchStart(event, this, 'client')" ontouchmove="handleTouchMove(event)" ontouchend="handleTouchEnd(event, this, 'client')">
                                    ${replyHtml}
                                    ${messageContentHtml}
                                    <div class="chat-time">${escapeHTML(m.time)} ${statusHtml}</div>
                                </div>
                            </div>
                        `;
                    });
                    
                    if(Object.keys(updatesToRead).length > 0) {
                        db.ref(`trackings/${currentClientPhone}/client_messages`).update(updatesToRead);
                    }

                    isChatInitialLoad = false;
                    area.innerHTML = html;
                    area.scrollTop = area.scrollHeight; 
                } else {
                    area.innerHTML = '<div style="font-size:12px; color:rgba(255,255,255,0.5); text-align:center; margin: auto; background:rgba(0,0,0,0.4); padding:6px 12px; border-radius:12px;">End-to-end encrypted chat started</div>';
                    isChatInitialLoad = false;
                }
            });
        }

        // --- 14. GLOBAL LOGOUT ---
        function systemLogout(e) {
            if(e) e.stopPropagation();
            if(geoWatchId !== null) toggleLocationBroadcast();
            
            db.ref('trackings').off(); 
            if(currentClientPhone) {
                db.ref(`trackings/${currentClientPhone}/location`).off();
                db.ref(`trackings/${currentClientPhone}/updates`).off();
                db.ref(`trackings/${currentClientPhone}/client_messages`).off();
            }
            if(currentAdminTargetPhone) {
                db.ref(`trackings/${currentAdminTargetPhone}/client_location`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/updates`).off();
                db.ref(`trackings/${currentAdminTargetPhone}/client_messages`).off();
            }
            
            currentAdminTargetPhone = ""; currentClientPhone = "";
            cancelReply('admin'); cancelReply('client');
            
            document.getElementById('client-map-iframe').src = "";
            document.getElementById('map-loader-text').style.display = 'block';
            document.getElementById('client-location-display').style.display = 'none';
            document.getElementById('ext-map-link').style.display = 'none';
            document.getElementById('client-gps-time').innerText = "Awaiting Signal...";
            
            showToast("Session Terminated", "error");
            switchView('view-gatekeeper');
        }
    </script>
</body>
</html>
