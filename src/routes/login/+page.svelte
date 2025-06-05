<script>
  import { supabase } from '$lib/supabaseClient';
  import { goto } from '$app/navigation';

  let email = '';
  let password = '';
  let errorMessage = '';

  async function handleLogin() {
    try {
      const { error } = await supabase.auth.signInWithPassword({
        email,
        password,
      });

      if (error) {
        errorMessage = error.message;
        return;
      }

      goto('/dashboard');
    } catch (error) {
      errorMessage = 'An unexpected error occurred.';
    }
  }
</script>

<main class="login-page">
  <div class="login-container">
    <div class="left-panel">
      <img src="/langit.jpeg" alt="Autopulse Logo" class="logo" />
      <h1>AUTOPULSE</h1>
      <p class="slogan">Perjalanan Lancar Dimulai dari Sini</p>
      <div class="triangle-pattern"></div>
    </div>
    <div class="right-panel">
      <div class="login-box">
        <h2>Hi, Admin Workshop</h2>
        <p class="instruction">Silahkan masukkan username dan password</p>
        <form on:submit|preventDefault={handleLogin}>
          <div class="form-group">
            <label for="email">Email:</label>
            <input id="email" type="email" bind:value={email} required placeholder="Enter your email" />
          </div>
          <div class="form-group">
            <label for="password">Password:</label>
            <input id="password" type="password" bind:value={password} required placeholder="Enter your password" />
          </div>
          {#if errorMessage}
            <p class="error">{errorMessage}</p>
          {/if}
          <button type="submit">Login</button>
        </form>
        <p class="footer-text">Terkenala masuk? hubungi kami</p>
      </div>
    </div>
  </div>
</main>

<style>
  .login-page {
    display: flex;
	justify-content: center;
	align-items: center;
    min-height: 100vh;
    width: 100vw;
    margin: 0;
    padding: 0;
    overflow: hidden;
  }

  .login-container {
    display: flex;
    width: 100%;
    height: 100%;
  }

  .left-panel {
    flex: 1;
    background: linear-gradient(135deg, #2c3e50 0%, #3498db 100%);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: relative;
    padding: 2rem;
    color: white;
  }

  .logo {
    width: 100px;
    height: auto;
    margin-bottom: 1rem;
  }

  h1 {
    font-size: 2.5rem;
    margin: 0.5rem 0;
    font-weight: bold;
  }

  .slogan {
    font-size: 1rem;
    margin-bottom: 2rem;
  }

  .triangle-pattern {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url('/.png') repeat;
    opacity: 0.3;
    z-index: 0;
  }

  .right-panel {
    flex: 1;
    background-color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 2rem;
  }

  .login-box {
    width: 100%;
    max-width: 400px;
    text-align: center;
  }

  h2 {
    font-size: 1.5rem;
    color: #2c3e50;
    margin-bottom: 0.5rem;
  }

  .instruction {
    color: #7f8c8d;
    margin-bottom: 1.5rem;
    font-size: 0.9rem;
  }

  .form-group {
    margin-bottom: 1rem;
    text-align: left;
  }

  .form-group label {
    display: block;
    margin-bottom: 0.25rem;
    color: #2c3e50;
    font-size: 0.9rem;
  }

  .form-group input {
    width: 100%;
    padding: 0.5rem;
    border: 1px solid #bdc3c7;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  .error {
    color: #e74c3c;
    text-align: center;
    margin-bottom: 1rem;
    font-size: 0.8rem;
  }

  button {
    width: 100%;
    padding: 0.75rem;
    background-color: #2c3e50;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 1rem;
    cursor: pointer;
  }

  button:hover {
    background-color: #34495e;
  }

  .footer-text {
    margin-top: 1rem;
    color: #7f8c8d;
    font-size: 0.8rem;
  }

  /* Responsivitas */
  @media (max-width: 768px) {
    .login-container {
      flex-direction: column;
    }

    .left-panel, .right-panel {
      flex: none;
      width: 100%;
      height: 50vh;
    }

    .triangle-pattern {
      height: 50vh;
    }

    .login-box {
      max-width: 90%;
    }
  }
</style>