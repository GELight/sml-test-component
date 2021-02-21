<svelte:options tag='sml-tryit' />

<script>
	import { ReliableTxtDocument, WsvParser } from '@gelight/sml-typescript-project';
	
	let content = `Hello "in the World" of SML`;
	let doc = [];
	let tabs = ['ReliableTxtDocument', 'WsvParser'];
	let selectedTab = 'WsvParser';
	let parserError = false;
	$: {
		if (selectedTab === 'WsvParser') {
			try {
				doc = new WsvParser().parseDocument(content);
				parserError = false;
			}
			catch (e) {
				parserError = true;
			}
		} else {
			try {
				doc = new ReliableTxtDocument(content).getLines();
				parserError = false;
			}
			catch (e) {
				parserError = true;
			}
		}
	}
	const getClass = (value) => {
		if (!value) return 'null';
		if (value === " ") return 'empty';
		return '';
	}
	const keydown =  (e) => {
		if (e.key === 'Tab') {
			e.preventDefault();
			var start = e.target.selectionStart;
			var end = e.target.selectionEnd;
			e.target.value = e.target.value.substring(0, start) + "\t" + e.target.value.substring(end);
			e.target.selectionStart = e.target.selectionEnd = start + 1;
		}
	};
</script>

<div class="sml-tryit">
	<textarea on:keydown="{keydown}" bind:value="{content}" class="{parserError ? 'parserError' : ''}"></textarea>
	<div class="tab-group">
		{#each tabs as tab}
			<label>
				<input type="radio" bind:group={selectedTab} value={tab}>
				<div class="tab">{tab}</div>
			</label>
		{/each}
	</div>
	<div class="sml-tryit-output">
		{#if doc.length}
			<table>
				{#each doc as line}
					<tr>
						{#each line as value}
							<td class="{getClass(value)}">{value}</td>
						{/each}
					</tr>
				{/each}
			</table>
		{/if}
	</div>
</div>

<style>
	:host {
		display: flex;
		background: var(--sml-tryit-bg);
		padding: var(--sml-tryit-padding);
		box-sizing: border-box;
	}
	* {
		box-sizing: border-box;
		position: relative;
	}
	.sml-tryit {
		display: flex;
		flex-direction: column;
		justify-content: stretch;
		align-items: flex-start;
		flex-wrap: wrap;
		gap: var(--sml-tryit-gap);
		flex: 1;
	}
	textarea {
		resize: none;
		text-align: left;
		color: var(--sml-tryit-textarea-color);
		background: var(--sml-tryit-textarea-bg);
		border: var(--sml-tryit-textarea-border);
		padding: var(--sml-tryit-textarea-padding);
		margin: var(--sml-tryit-textarea-margin);
		width: var(--sml-tryit-textarea-width);
		height: var(--sml-tryit-textarea-height);
		box-shadow: var(--sml-tryit-textarea-box-shadow);
		line-height: var(--sml-tryit-textarea-line-height);
	}
	textarea:focus {
		outline: none;
	}
	textarea.parserError {
		background: var(--sml-tryit-textarea-error-bg);
		border: var(--sml-tryit-textarea-error-border);
		color: var(--sml-tryit-textarea-error-color);
	}
	textarea.parserError::after {
		content: 'WsvParserException'
	}
	.tab-group {
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}
	.tab {
		background: var(--sml-tryit-tab-bg);
		padding: var(--sml-tryit-tab-padding);
		color: var(--sml-tryit-tab-color);
		border-bottom: var(--sml-tryit-tab-border-bottom);
	}
	.tab:hover {
		cursor: pointer;
		background: var(--sml-tryit-tab-hover-bg);
		color: var(--sml-tryit-tab-hover-color);
	}
	[type="radio"] {
		display: none;
	}
	[type="radio"]:checked + .tab {
		background: var(--sml-tryit-tab-checked-bg);
		color: var(--sml-tryit-tab-checked-color);
	}
	.sml-tryit-output {
		overflow: auto;
		width: 100%;
		max-width: 100%;
		min-height: var(--sml-tryit-textarea-height);
		flex: 1;
		border-top: var(--sml-tryit-output-border-top);
		background: var(--sml-tryit-output-bg);
		padding: var(--sml-tryit-output-padding);
		margin-top: var(--sml-tryit-output-margin-top);
	}
	table {
		display: block;
	}
	td {
		text-align: left;
		border: var(--sml-tryit-td-border);
		color: var(--sml-tryit-td-color);
		padding: var(--sml-tryit-td-padding);
		background: var(--sml-tryit-td-bg);
		font-size: var(--sml-tryit-td-font-size);
		font-family: var(--sml-tryit-td-font-family);
	}
	td.empty {
		background: var(--sml-tryit-td-empty);
	}
	td.null {
		background: var(--sml-tryit-td-null);
	}
</style>