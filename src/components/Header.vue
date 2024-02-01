<template>
	<header>
		<h1>Von</h1>
		<nav>
			<p>Credits: 500</p>
		</nav>
	</header>
</template>

<style lang="less" scoped>
header {
	background-color: var(--primary);
	backdrop-filter: blur(8px);
	color: #fff;
	padding: 8px;
	height: 48px;

	display: flex;
	align-items: center;
	justify-content: space-between;

	gap: 8px;

	h1 {
		font-size: 20px;
		margin: 0;
		padding: 0;
		text-align: left;
	}

	nav, p {
		font-size: 16px;
		margin: 0;
		padding: 0;
	}
}
</style>