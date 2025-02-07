<h1 class="notification">
	<slot />
</h1>

<style lang="scss">
	.notification {
    position: fixed;
    bottom: 20px;
    right: 20px;
    background-color: rgba(105, 162, 236, 0.9);
    color: white;
    padding: 10px 15px;
    border-radius: 5px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
    z-index: 1000;
		font-size: 16px;
  }
</style>