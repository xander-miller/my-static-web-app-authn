<script>
  import { Link } from 'svelte-routing';
  import { onMount } from 'svelte';

  const providers = [{text: 'Twitter', value: 'twitter'}, {text: 'Github', value: 'github'}, {text: 'Azure Active Record', value: 'aad'}, {text: 'Bonjour Staging', value: 'bonjour'}];
  const redirect = window.location.pathname;
  let userInfo = undefined;

  onMount(async () => (userInfo = await getUserInfo()));

  async function getUserInfo() {
    try {
      const response = await fetch('/.auth/me');
      const payload = await response.json();
      const { clientPrincipal } = payload;
      return clientPrincipal;
    } catch (error) {
      console.error('No profile could be found');
      return undefined;
    }
  }
  function getProps({ href, isPartiallyCurrent, isCurrent }) {
    const isActive = href === '/' ? isCurrent : isPartiallyCurrent || isCurrent;

    // The object returned here is spread on the anchor element's attributes
    if (isActive) {
      return { class: 'router-link-active' };
    }
    return {};
  }
</script>

<div class="column is-2">
  <nav class="menu">
    <p class="menu-label">Menu</p>
    <ul class="menu-list">
      <Link to="/products" {getProps}>Products</Link>
      <Link to="/about" {getProps}>About</Link>
    </ul>
  </nav>
  <nav class="menu auth">
    <p class="menu-label">Auth</p>
    <div class="menu-list auth">
      {#if !userInfo}
        {#each providers as provider (provider)}
          <a href={`/.auth/login/${provider.value}?post_login_redirect_uri=${redirect}`}>
            {provider.text}
          </a>
        {/each}
      {/if}
      {#if true}
        <a href={`/.auth/logout?post_logout_redirect_uri=${redirect}`}>
          Logout
        </a>
      {/if}
    </div>
  </nav>
  {#if userInfo}
    <div class="user">
      <p>Welcome</p>
      <p>{userInfo && userInfo.userDetails}</p>
      <p>{userInfo && userInfo.identityProvider}</p>
    </div>
  {/if}
</div>
