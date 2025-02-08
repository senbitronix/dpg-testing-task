<script lang="ts">
	/**
	 * Dependence
	 */
	import PostsApi from '$lib/api/methods/posts';
	import { getReasonPhrase } from 'http-status-codes';

	/**
	 * Components
	 */
	import Feed from '$lib/components/feed/Feed.svelte';
	import Loader from '$lib/components/shared/Loader.svelte';
	import ErrorComponent from '$lib/components/shared/Error.svelte';
	import NotificationComponent from '$lib/components/shared/Notification.svelte';


	let loadingFromCache = false;

	/**
	 * Load From Cache
	 * @description Загружаем посты из кеша
	 */
	const loadFromCache = () => {
    const cachedPosts = localStorage.getItem('posts');
    return cachedPosts ? JSON.parse(cachedPosts) : [];
  };

	/**
	 * Show Notificatin
	 * @description Показываем уведомление
	 * @param {number} value - Current value
	 */
	const showNotification = (ms = 3000) => {
    loadingFromCache = true;	
		setTimeout(()=> {
			loadingFromCache = false
		}, ms)
  };

	/**
	 * Load data
	 * @description Метод для получения постов
	 */
	const loadData = async () => {
		const response = await PostsApi.getAll();

		if (!response.success) {
			if (response.data.isNetworkError) {
				const cachedPosts = loadFromCache();
				if (cachedPosts.length) {					
					showNotification()
					return cachedPosts
				} else {
					return []
				}
			} else {
				throw new Error(getReasonPhrase(response.data.code));
			}
		}		

		localStorage.setItem('posts', JSON.stringify(response.data));

		return response.data;
	};

</script>

{#await loadData()}
	<Loader />
{:then posts}
	{#if posts.length > 0}
		{#if loadingFromCache}
			<NotificationComponent>
				Посты загружены из кеша
			</NotificationComponent>
		{/if}
    <Feed {posts} />
  {:else}
    <p>Нет данных для отображения</p>
  {/if}
{:catch e}
	<ErrorComponent>
		{e}
	</ErrorComponent>
{/await}