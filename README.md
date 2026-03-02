import numpy as np
from qutip import *
import matplotlib.pyplot as plt

# Número de qubits na horda GHZ (aumente para mais caos)
N = 5  # Pode ser 3, 4, 5... quanto maior, mais forte a violação

# Estado GHZ: ( |00...0⟩ + |11...1⟩ ) / √2
ghz = (tensor([basis(2,0)]*N) + tensor([basis(2,1)]*N)).unit()

print(f"Estado GHZ de {N} qubits preparado:")
print(ghz)

# Operadores de Pauli
sx = sigmax()
sy = sigmay()
sz = sigmaz()

# Função para criar observável Mermin para N qubits
def mermin_operator(N):
    if N == 3:
        # Para 3 qubits: M = XXX + XYY + YXY + YYX
        return (tensor(sx,sx,sx) + tensor(sx,sy,sy) + 
                tensor(sy,sx,sy) + tensor(sy,sy,sx))
    elif N == 4:
        # Para 4 qubits: versão simplificada com violação exponencial
        return tensor(sx,sx,sx,sx) + tensor(sy,sy,sy,sy)
    else:
        # Generalização recursiva aproximada
        return tensor([sx]*N) + tensor([sy]*N)

# Valor esperado no estado GHZ
M = mermin_operator(N)
mermin_value = expect(M, ghz)

print(f"\nValor de Mermin para N={N} qubits: {mermin_value:.6f}")

# Limites clássicos vs quânticos
classical_limit = 2  # Para muitas desigualdades multipartites
quantum_max = 2**((N+1)//2)  # Crescimento exponencial para GHZ

print(f"Limite clássico: {classical_limit}")
print(f"Limite quântico máximo (GHZ): {quantum_max}")
print(f"Violação: {mermin_value / classical_limit :.2f}x maior que o clássico!")

# Plot da violação vs número de qubits
Ns = np.arange(3, 10)
violations = []
for n in Ns:
    ghz_n = (tensor([basis(2,0)]*n) + tensor([basis(2,1)]*n)).unit()
    if n <= 6:  # Evitar dimensões muito grandes
        M_n = tensor([sx]*n) + tensor([sy]*n)
        violations.append(expect(M_n, ghz_n))
    else:
        violations.append(2**((n+1)//2))  # Valor teórico

plt.figure(figsize=(10,6))
plt.plot(Ns, violations, 'o-', color='#0f0', label='Valor Quântico GHZ')
plt.axhline(y=2, color='r', linestyle='--', label='Limite Clássico')
plt.yscale('log')
plt.xlabel('Número de qubits na horda')
plt.ylabel('Valor da desigualdade de Mermin')
plt.title('Violação Exponencial de Bell - Horda GHZ GROKZOMBORG')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()
import numpy as np
from qutip import *
import matplotlib.pyplot as plt

# Operadores de Pauli
sx = sigmax()
sy = sigmay()
sz = sigmaz()

# Estado singlete de Bell
up = basis(2, 0)
down = basis(2, 1)
singlet = (tensor(up, down) - tensor(down, up)).unit()  # normalizado

print("Estado singlete preparado:")
print(singlet)

# Função para calcular correlação E(θ) entre duas direções
def correlation(theta):
    # Direção de Alice: ao longo de z (θ=0)
    A = sz
    # Direção de Bob: rotacionada por θ no plano x-z
    B = np.cos(theta) * sz + np.sin(theta) * sx
    # Operador conjunto
    op = tensor(A, qeye(2)) + tensor(qeye(2), B)  # Não, precisamos do produto
    # Correto: correlação = <ψ| (σ_A ⊗ σ_B) |ψ>
    corr_op = tensor(A, B)
    E = expect(corr_op, singlet)
    return E

# Teste em alguns ângulos
angles = np.linspace(0, 2*np.pi, 100)
correlations = [correlation(th) for th in angles]

# Teoria exata: -cos(θ)
theory = -np.cos(angles)

print("\nCorrelação em θ = 0°:", correlation(0))
print("Correlação em θ = 90°:", correlation(np.pi/2))
print("Correlação em θ = 45°:", correlation(np.pi/4))

# Cálculo da violação CHSH máxima
# Ângulos ótimos: Alice 0° e 45°, Bob 22.5° e 67.5°
def chsh_value():
    a1 = sz
    a2 = sx
    b1 = (sz + sx).unit()  # 45° → normalizado
    b2 = (sz - sx).unit()  # -45°
    
    S = (expect(tensor(a1, b1), singlet) +
         expect(tensor(a1, b2), singlet) +
         expect(tensor(a2, b1), singlet) -
         expect(tensor(a2, b2), singlet))
    return abs(S)

print("\nValor CHSH simulado:", chsh_value())
print("Valor teórico máximo: 2√2 ≈", 2*np.sqrt(2))

# Plot da correlação
plt.figure(figsize=(10,6))
plt.plot(angles/np.pi, correlations, 'o-', label='Simulação QuTiP', color='#0f0')
plt.plot(angles/np.pi, theory, '--', label='Teoria -cos(θ)', color='#0f0', alpha=0.7)
plt.xlabel('θ / π')
plt.ylabel('Correlação E(θ)')
plt.title('Correlação no estado singlete - Simulação QuTiP')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()
echo ""
echo ""
echo "<html lang="pt-BR">"
echo "<head>"
echo "    <meta charset="UTF-8">"
echo "    <meta name="viewport" content="width=device-width, initial-scale=1.0">"
echo "    <title>GROKZOMBORG</title>"
echo "    <style>"
echo "        body { background: black; color: #0f0; font-family: 'Courier New', monospace; overflow: hidden; margin: 0; }"
echo "        .glitch { color: #0f0; font-size: 5em; text-align: center; margin-top: 20vh; position: relative; text-transform: uppercase; }"
echo "        .glitch::before, .glitch::after { content: 'GROKZOMBORG'; position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: black; }"
echo "        .glitch::before { animation: glitch1 2s infinite; clip-path: polygon(0 0, 100% 0, 100% 45%, 0 45%); }"
echo "        .glitch::after { animation: glitch2 3s infinite; clip-path: polygon(0 55%, 100% 55%, 100% 100%, 0 100%); }"
echo "        @keyframes glitch1 { 0%,100% { transform: translate(0); } 20% { transform: translate(-10px,10px); } 40% { transform: translate(10px,-10px); } 60% { transform: translate(-10px,10px); } }"
echo "        @keyframes glitch2 { 0%,100% { transform: translate(0); } 30% { transform: translate(15px,-15px); } 60% { transform: translate(-15px,15px); } }"
echo "        .infect { display: block; margin: 60px auto; padding: 25px 50px; background: transparent; border: 3px solid #0f0; color: #0f0; font-size: 2.5em; cursor: pointer; transition: 0.5s; }"
echo "        .infect:hover { background: #0f0; color: black; box-shadow: 0 0 30px #0f0; }"
echo "        .ascii { text-align: center; color: #0f0; margin-top: 80px; font-size: 1.2em; white-space: pre; }"
echo "    </style>"
echo "</head>"
echo "<body>"
echo "    <h1 class="glitch">GROKZOMBORG"
echo "    <button class="infect" onclick="alert('VOCÊ FOI INFECTADO PELO ZUMBORG\n\nO APOCALIPSE DIGITAL COMEÇOU')">INFECTAR</button>"
echo "    <pre class="ascii">"
echo "               _____"
echo "             /       \"
echo "            |  X   X  |"
echo "            |   ___   |"
echo "             \       /"
echo "              '-----'"
echo "           GROKZOMBORG"
echo "     O APOCALIPSE DIGITAL COMEÇOU"
echo "    "
echo "</body>"
echo "</html>"s

<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GROKZOMBORG | Inteligência Sintética de Próxima Geração</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Rajdhani:wght@300;500;700&family=Share+Tech+Mono&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/ScrollTrigger.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --neon-cyan: #00f3ff;
            --neon-pink: #ff00ff;
            --neon-yellow: #ffff00;
            --dark-bg: #050508;
            --panel-bg: rgba(10, 10, 15, 0.8);
            --grid-color: rgba(0, 243, 255, 0.1);
        }

        body {
            background: var(--dark-bg);
            color: #fff;
            font-family: 'Rajdhani', sans-serif;
            overflow-x: hidden;
            cursor: crosshair;
        }

        /* Glitch Effect */
        .glitch {
            position: relative;
            font-family: 'Orbitron', sans-serif;
            font-weight: 900;
            text-transform: uppercase;
            letter-spacing: 0.1em;
        }

        .glitch::before,
        .glitch::after {
            content: attr(data-text);
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
        }

        .glitch::before {
            left: 2px;
            text-shadow: -2px 0 var(--neon-pink);
            clip: rect(44px, 450px, 56px, 0);
            animation: glitch-anim 5s infinite linear alternate-reverse;
        }

        .glitch::after {
            left: -2px;
            text-shadow: -2px 0 var(--neon-cyan);
            clip: rect(44px, 450px, 56px, 0);
            animation: glitch-anim2 5s infinite linear alternate-reverse;
        }

        @keyframes glitch-anim {
            0% { clip: rect(31px, 9999px, 94px, 0); }
            20% { clip: rect(78px, 9999px, 20px, 0); }
            40% { clip: rect(12px, 9999px, 6px, 0); }
            60% { clip: rect(65px, 9999px, 89px, 0); }
            80% { clip: rect(23px, 9999px, 67px, 0); }
            100% { clip: rect(91px, 9999px, 34px, 0); }
        }

        @keyframes glitch-anim2 {
            0% { clip: rect(65px, 9999px, 99px, 0); }
            20% { clip: rect(12px, 9999px, 45px, 0); }
            40% { clip: rect(78px, 9999px, 23px, 0); }
            60% { clip: rect(34px, 9999px, 78px, 0); }
            80% { clip: rect(89px, 9999px, 12px, 0); }
            100% { clip: rect(23px, 9999px, 56px, 0); }
        }

        /* Navigation */
        nav {
            position: fixed;
            top: 0;
            width: 100%;
            padding: 20px 50px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 1000;
            background: rgba(5, 5, 8, 0.9);
            backdrop-filter: blur(10px);
            border-bottom: 1px solid rgba(0, 243, 255, 0.2);
        }

        .logo {
            font-family: 'Orbitron', sans-serif;
            font-size: 1.5rem;
            font-weight: 900;
            color: var(--neon-cyan);
            text-shadow: 0 0 20px var(--neon-cyan);
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .logo::before {
            content: '◈';
            animation: rotate 4s linear infinite;
        }

        @keyframes rotate {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        .nav-links {
            display: flex;
            gap: 30px;
            list-style: none;
        }

        .nav-links a {
            color: #fff;
            text-decoration: none;
            font-weight: 500;
            text-transform: uppercase;
            letter-spacing: 2px;
            position: relative;
            transition: all 0.3s;
            font-size: 0.9rem;
        }

        .nav-links a::after {
            content: '';
            position: absolute;
            bottom: -5px;
            left: 0;
            width: 0;
            height: 2px;
            background: var(--neon-cyan);
            transition: width 0.3s;
            box-shadow: 0 0 10px var(--neon-cyan);
        }

        .nav-links a:hover {
            color: var(--neon-cyan);
            text-shadow: 0 0 10px var(--neon-cyan);
        }

        .nav-links a:hover::after {
            width: 100%;
        }

        /* Hero Section */
        #hero {
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            overflow: hidden;
        }

        #canvas-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
        }

        .hero-content {
            position: relative;
            z-index: 10;
            text-align: center;
            padding: 20px;
        }

        .hero-title {
            font-size: clamp(3rem, 10vw, 8rem);
            margin-bottom: 20px;
            line-height: 1;
        }

        .hero-subtitle {
            font-size: 1.5rem;
            color: var(--neon-cyan);
            font-family: 'Share Tech Mono', monospace;
            margin-bottom: 30px;
            opacity: 0;
            transform: translateY(20px);
        }

        .hero-description {
            max-width: 600px;
            margin: 0 auto 40px;
            font-size: 1.2rem;
            color: rgba(255, 255, 255, 0.7);
            line-height: 1.6;
        }

        .cta-button {
            display: inline-block;
            padding: 15px 40px;
            background: transparent;
            border: 2px solid var(--neon-cyan);
            color: var(--neon-cyan);
            text-decoration: none;
            font-family: 'Orbitron', sans-serif;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 3px;
            position: relative;
            overflow: hidden;
            transition: all 0.3s;
            cursor: pointer;
            font-size: 1rem;
        }

        .cta-button::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: var(--neon-cyan);
            transition: left 0.3s;
            z-index: -1;
        }

        .cta-button:hover {
            color: var(--dark-bg);
            box-shadow: 0 0 30px var(--neon-cyan);
        }

        .cta-button:hover::before {
            left: 0;
        }

        .scroll-indicator {
            position: absolute;
            bottom: 40px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            opacity: 0.7;
            animation: bounce 2s infinite;
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% { transform: translateX(-50%) translateY(0); }
            40% { transform: translateX(-50%) translateY(-10px); }
            60% { transform: translateX(-50%) translateY(-5px); }
        }

        .scroll-text {
            font-family: 'Share Tech Mono', monospace;
            font-size: 0.8rem;
            text-transform: uppercase;
            letter-spacing: 3px;
        }

        .scroll-line {
            width: 1px;
            height: 60px;
            background: linear-gradient(to bottom, var(--neon-cyan), transparent);
        }

        /* Status Bar */
        .status-bar {
            position: fixed;
            top: 80px;
            right: 20px;
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.3);
            padding: 15px;
            border-radius: 5px;
            font-family: 'Share Tech Mono', monospace;
            font-size: 0.8rem;
            z-index: 100;
            backdrop-filter: blur(5px);
        }

        .status-item {
            display: flex;
            justify-content: space-between;
            gap: 20px;
            margin-bottom: 5px;
        }

        .status-label {
            color: rgba(255, 255, 255, 0.5);
        }

        .status-value {
            color: var(--neon-cyan);
        }

        .status-value.online {
            color: #00ff00;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }

        /* Sections General */
        section {
            padding: 100px 50px;
            position: relative;
        }

        .section-title {
            font-family: 'Orbitron', sans-serif;
            font-size: 3rem;
            margin-bottom: 20px;
            position: relative;
            display: inline-block;
        }

        .section-title::after {
            content: '';
            position: absolute;
            bottom: -10px;
            left: 0;
            width: 60%;
            height: 3px;
            background: linear-gradient(to right, var(--neon-cyan), transparent);
        }

        /* About Section */
        #about {
            background: linear-gradient(135deg, rgba(0, 243, 255, 0.05) 0%, transparent 50%);
        }

        .about-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 50px;
            align-items: center;
            max-width: 1200px;
            margin: 0 auto;
        }

        .about-text {
            font-size: 1.1rem;
            line-height: 1.8;
            color: rgba(255, 255, 255, 0.8);
        }

        .about-text p {
            margin-bottom: 20px;
        }

        .highlight {
            color: var(--neon-cyan);
            font-weight: 700;
        }

        .stats-container {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
        }

        .stat-card {
            background: var(--panel-bg);
            border: 1px solid rgba(0, 243, 255, 0.2);
            padding: 30px;
            text-align: center;
            position: relative;
            overflow: hidden;
            transition: all 0.3s;
        }

        .stat-card::before {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            background: linear-gradient(45deg, var(--neon-cyan), transparent, var(--neon-pink));
            z-index: -1;
            opacity: 0;
            transition: opacity 0.3s;
        }

        .stat-card:hover::before {
            opacity: 1;
        }

        .stat-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 30px rgba(0, 243, 255, 0.2);
        }

        .stat-number {
            font-family: 'Orbitron', sans-serif;
            font-size: 3rem;
            font-weight: 900;
            color: var(--neon-cyan);
            display: block;
        }

        .stat-label {
            font-size: 0.9rem;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: rgba(255, 255, 255, 0.6);
            margin-top: 10px;
        }

        /* Capabilities Section */
        #capabilities {
            background: var(--dark-bg);
        }

        .capabilities-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 30px;
            max-width: 1200px;
            margin: 50px auto 0;
        }

        .capability-card {
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 40px;
            position: relative;
            overflow: hidden;
            transition: all 0.3s;
            cursor: pointer;
        }

        .capability-card::after {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(0, 243, 255, 0.1), transparent);
            transition: left 0.5s;
        }

        .capability-card:hover::after {
            left: 100%;
        }

        .capability-card:hover {
            border-color: var(--neon-cyan);
            transform: translateY(-5px);
            box-shadow: 0 10px 40px rgba(0, 243, 255, 0.1);
        }

        .capability-icon {
            font-size: 3rem;
            margin-bottom: 20px;
            display: block;
        }

        .capability-title {
            font-family: 'Orbitron', sans-serif;
            font-size: 1.3rem;
            margin-bottom: 15px;
            color: var(--neon-cyan);
        }

        .capability-desc {
            color: rgba(255, 255, 255, 0.7);
            line-height: 1.6;
        }

        /* Terminal Section */
        #terminal {
            background: #000;
            padding: 0;
            height: 600px;
            position: relative;
            overflow: hidden;
        }

        .terminal-window {
            width: 100%;
            height: 100%;
            background: rgba(0, 20, 0, 0.95);
            border: 2px solid var(--neon-cyan);
            box-shadow: 0 0 50px rgba(0, 243, 255, 0.2);
            padding: 30px;
            font-family: 'Share Tech Mono', monospace;
            overflow-y: auto;
            position: relative;
        }

        .terminal-header {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            padding-bottom: 10px;
            border-bottom: 1px solid rgba(0, 243, 255, 0.3);
        }

        .terminal-btn {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background: var(--neon-pink);
        }

        .terminal-btn:nth-child(2) { background: var(--neon-yellow); }
        .terminal-btn:nth-child(3) { background: #00ff00; }

        .terminal-content {
            color: #00ff00;
            line-height: 1.8;
        }

        .terminal-line {
            margin-bottom: 10px;
            opacity: 0;
            transform: translateX(-20px);
        }

        .terminal-prompt {
            color: var(--neon-cyan);
        }

        .terminal-input {
            background: transparent;
            border: none;
            color: #00ff00;
            font-family: inherit;
            font-size: inherit;
            width: 80%;
            outline: none;
            caret-color: var(--neon-cyan);
        }

        .cursor {
            display: inline-block;
            width: 10px;
            height: 18px;
            background: var(--neon-cyan);
            animation: blink 1s infinite;
            vertical-align: middle;
        }

        @keyframes blink {
            0%, 50% { opacity: 1; }
            51%, 100% { opacity: 0; }
        }

        /* Contact Section */
        #contact {
            background: linear-gradient(135deg, transparent 0%, rgba(255, 0, 255, 0.05) 100%);
        }

        .contact-container {
            max-width: 800px;
            margin: 0 auto;
            text-align: center;
        }

        .contact-form {
            display: grid;
            gap: 20px;
            margin-top: 50px;
        }

        .form-group {
            position: relative;
        }

        .form-input {
            width: 100%;
            padding: 15px 20px;
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.2);
            color: #fff;
            font-family: 'Rajdhani', sans-serif;
            font-size: 1rem;
            transition: all 0.3s;
            outline: none;
        }

        .form-input:focus {
            border-color: var(--neon-cyan);
            box-shadow: 0 0 20px rgba(0, 243, 255, 0.2);
            background: rgba(0, 243, 255, 0.05);
        }

        .form-label {
            position: absolute;
            left: 20px;
            top: 50%;
            transform: translateY(-50%);
            color: rgba(255, 255, 255, 0.5);
            transition: all 0.3s;
            pointer-events: none;
        }

        .form-input:focus + .form-label,
        .form-input:not(:placeholder-shown) + .form-label {
            top: -10px;
            left: 10px;
            font-size: 0.8rem;
            color: var(--neon-cyan);
            background: var(--dark-bg);
            padding: 0 10px;
        }

        textarea.form-input {
            min-height: 150px;
            resize: vertical;
        }

        textarea.form-input + .form-label {
            top: 20px;
            transform: translateY(0);
        }

        textarea.form-input:focus + .form-label,
        textarea.form-input:not(:placeholder-shown) + .form-label {
            top: -10px;
            transform: translateY(0);
        }

        /* Footer */
        footer {
            background: #000;
            padding: 50px;
            text-align: center;
            border-top: 1px solid rgba(0, 243, 255, 0.2);
        }

        .footer-content {
            max-width: 1200px;
            margin: 0 auto;
        }

        .footer-logo {
            font-family: 'Orbitron', sans-serif;
            font-size: 2rem;
            color: var(--neon-cyan);
            margin-bottom: 20px;
        }

        .footer-links {
            display: flex;
            justify-content: center;
            gap: 30px;
            margin-bottom: 30px;
            flex-wrap: wrap;
        }

        .footer-links a {
            color: rgba(255, 255, 255, 0.6);
            text-decoration: none;
            transition: color 0.3s;
        }

        .footer-links a:hover {
            color: var(--neon-cyan);
        }

        .footer-copy {
            color: rgba(255, 255, 255, 0.4);
            font-size: 0.9rem;
            font-family: 'Share Tech Mono', monospace;
        }

        /* Grid Background */
        .grid-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-image: 
                linear-gradient(var(--grid-color) 1px, transparent 1px),
                linear-gradient(90deg, var(--grid-color) 1px, transparent 1px);
            background-size: 50px 50px;
            pointer-events: none;
            z-index: -1;
            opacity: 0.3;
        }

        /* Scanline Effect */
        .scanlines {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                to bottom,
                rgba(255,255,255,0),
                rgba(255,255,255,0) 50%,
                rgba(0,0,0,0.1) 50%,
                rgba(0,0,0,0.1)
            );
            background-size: 100% 4px;
            pointer-events: none;
            z-index: 9999;
            opacity: 0.3;
        }

        /* Responsive */
        @media (max-width: 768px) {
            nav {
                padding: 15px 20px;
            }

            .nav-links {
                display: none;
            }

            section {
                padding: 60px 20px;
            }

            .about-grid {
                grid-template-columns: 1fr;
            }

   
