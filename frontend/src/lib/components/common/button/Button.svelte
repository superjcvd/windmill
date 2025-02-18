<script lang="ts">
	import { createEventDispatcher } from 'svelte'
	import { ButtonType } from './model'
	import { twMerge } from 'tailwind-merge'
	import ButtonDropdown from './ButtonDropdown.svelte'
	import { MenuItem } from '@rgossiaux/svelte-headlessui'
	import { classNames, getModifierKey } from '$lib/utils'
	import { Loader2 } from 'lucide-svelte'

	export let size: ButtonType.Size = 'md'
	export let spacingSize: ButtonType.Size = size
	export let color: ButtonType.Color | string = 'blue'
	export let variant: ButtonType.Variant = 'contained'
	export let btnClasses: string = ''
	export let wrapperClasses: string = ''
	export let wrapperStyle: string = ''
	export let disabled: boolean = false
	export let href: string | undefined = undefined
	export let target: '_self' | '_blank' | undefined = undefined
	export let iconOnly: boolean = false
	export let loadUntilNav: boolean = false

	export let clickableWhileLoading = false

	export let element: ButtonType.Element | undefined = undefined
	export let id: string = ''
	export let nonCaptureEvent: boolean = false
	export let propagateEvent: boolean = false
	export let loading = false
	export let title: string | undefined = undefined
	export let style: string = ''
	export let download: string | undefined = undefined
	export let portalTarget: string | undefined = undefined
	export let startIcon: ButtonType.Icon | undefined = undefined
	export let endIcon: ButtonType.Icon | undefined = undefined
	export let shortCut:
		| { key?: string; hide?: boolean; Icon?: any; withoutModifier?: boolean }
		| undefined = undefined

	type MenuItem = {
		label: string
		onClick?: (e?: Event) => void
		href?: string
		icon?: any
	}
	export let dropdownItems: MenuItem[] | (() => MenuItem[]) | undefined = undefined

	function computeDropdowns(): MenuItem[] | undefined {
		if (typeof dropdownItems === 'function') {
			return dropdownItems()
		} else {
			return dropdownItems
		}
	}

	export function focus() {
		element?.focus({})
	}

	const dispatch = createEventDispatcher()
	// Order of classes: border, border modifier, bg, bg modifier, text, text modifier, everything else
	const colorVariants: Record<ButtonType.Color, Record<ButtonType.Variant, string>> = {
		none: {
			border: '',
			contained: '',
			divider: ''
		},

		blue: {
			border:
				'border-frost-500 dark:border-frost-300 hover:border-frost-700 dark:hover:border-frost-400 focus-visible:border-frost-700 bg-surface hover:bg-frost-100 dark:hover:bg-frost-900 focus-visible:bg-frost-100 focus-visible:dark:text-frost-100 dark:focus-visible:bg-frost-900 text-frost-500 dark:text-frost-300 dark:hover:text-frost-400 hover:text-frost-700 focus-visible:text-frost-700 focus-visible:ring-frost-300',
			contained:
				'bg-frost-500 hover:bg-frost-700 focus-visible:bg-frost-700 text-white focus-visible:ring-frost-300 dark:bg-frost-500/90 dark:hover:bg-frost-600/90',
			divider: 'divide-x divide-frost-600'
		},
		marine: {
			border:
				'border-marine-300 dark:border-marine-200 hover:border-marine-500 dark:hover:border-marine-400 focus-visible:border-marine-500 bg-surface hover:bg-marine-500 dark:hover:bg-marine-400 focus-visible:bg-marine-100 focus-visible:dark:text-marine-50 dark:focus-visible:bg-marine-500 text-marine-50 dark:text-marine-50 dark:hover:text-marine-50 hover:text-marine-50 focus-visible:text-marine-300 focus-visible:ring-marine-200',
			contained:
				'bg-marine-300 hover:bg-marine-500 focus-visible:bg-marine-500 text-gray-50 focus-visible:ring-marine-200 dark:bg-marine-200 dark:hover:bg-marine-400',
			divider: 'divide-x divide-marine-500'
		},
		red: {
			border:
				'border-red-600/60 hover:border-red-600 bg-surface hover:bg-red-100 text-red-600 hover:text-red-700 focus-visible:ring-red-300 dark:border-red-400/90 dark:text-red-400 dark:hover:border-red-400 dark:hover:bg-red-500/60 dark:hover:text-red-100',
			contained:
				'bg-red-600 hover:bg-red-600 text-white focus-visible:ring-red-300  dark:border-red-400/70 dark:bg-red-700/90 dark:hover:bg-red-900 dark:hover:border-red-300 dark:text-primary',
			divider: 'divide-x divide-red-700'
		},
		green: {
			border:
				'border-green-600 hover:border-green-700 bg-surface hover:bg-green-100 text-green-600 hover:text-green-700 focus-visible:ring-green-300 dark:hover:bg-green-800 dark:border-green-400/90',
			contained:
				'bg-green-600 hover:bg-green-700 text-white focus-visible:ring-green-300 dark:bg-green-700/90 dark:hover:bg-green-800',
			divider: 'divide-x divide-green-700'
		},
		dark: {
			border:
				'border-marine-300 bg-surface hover:bg-surface-hover focus-visible:bg-surface-hover text-primary hover:text-secondary focus-visible:text-secondary focus-visible:ring-surface-selected-inverse dark:border-marine-200',
			contained:
				'bg-marine-400 hover:bg-marine-200 focus-visible:bg-surface-hover-inverse text-primary-inverse focus-visible:ring-surface-selected-inverse dark:bg-marine-50 dark:hover:bg-marine-50/70 dark:text-primary-inverse',
			divider: 'divide-x divide-gray-800 dark:divide-gray-200'
		},
		gray: {
			border:
				'border-gray-500 dark:border-gray-300 hover:border-gray-700 dark:hover:border-gray-400 focus-visible:border-gray-700 bg-surface hover:bg-gray-100 dark:hover:bg-gray-900 focus-visible:bg-gray-100 focus-visible:dark:text-gray-100 dark:focus-visible:bg-gray-900 text-gray-500 dark:text-gray-300 dark:hover:text-gray-400 hover:text-gray-700 focus-visible:text-gray-700 focus-visible:ring-gray-300',
			contained:
				'bg-gray-500 hover:bg-gray-700 focus-visible:bg-gray-700 text-white focus-visible:ring-gray-300',
			divider: 'divide-x divide-gray-600'
		},
		light: {
			border:
				'border  bg-surface hover:bg-surface-hover focus-visible:bg-surface-hover text-primary hover:text-secondary focus-visible:text-secondary focus-visible:ring-surface-selected',
			contained:
				'bg-surface border-transparent hover:bg-surface-hover focus-visible:bg-surface-hover text-primary focus-visible:ring-surface-selected',
			divider: 'divide-x divide-gray-200 dark:divide-gray-700'
		}
	}

	async function onClick(event: MouseEvent) {
		if (!nonCaptureEvent) {
			event.preventDefault()
			if (!propagateEvent) {
				// by default events are not propagated, added this prop so that we can
				event.stopPropagation()
			}
			dispatch('click', event)
		}
	}

	function getColorClass(color, variant) {
		if (color in colorVariants) {
			return colorVariants[color][variant]
		} else {
			return color
		}
	}

	$: buttonClass = twMerge(
		'w-full',
		getColorClass(color, variant),
		variant === 'border' ? 'border' : '',
		ButtonType.FontSizeClasses[size],
		ButtonType.SpacingClasses[spacingSize][variant],
		'focus-visible:ring-2 font-semibold',
		dropdownItems && dropdownItems.length > 0 ? 'rounded-l-md h-full' : 'rounded-md',
		'justify-center items-center text-center whitespace-nowrap inline-flex gap-2',
		btnClasses,
		'active:opacity-80 transition-all',
		disabled ? '!bg-surface-disabled !text-tertiary cursor-not-allowed' : '',
		loading ? 'cursor-wait' : ''
	)

	const iconMap = {
		xs2: 14,
		xs: 14,
		sm: 16,
		md: 16,
		lg: 18,
		xl: 18
	}

	const iconOnlyPadding = {
		xs2: 'm-[1px] qhd:m-[1.125px]',
		xs: 'm-[1px] qhd:m-[1.125px]',
		sm: 'm-[2px] qhd:m-[2.25px]',
		md: 'm-[2px] qhd:m-[2.25px]',
		lg: 'm-[5px] qhd:m-[5.625px]',
		xl: 'm-[5px] qhd:m-[5.625px]'
	}

	let innerWidth = 0
	$: lucideIconSize = (iconMap[size] ?? 12) * (innerWidth > 2500 ? 1.125 : 1)
</script>

<svelte:window bind:innerWidth />

<div
	class="{dropdownItems && dropdownItems.length > 0 && variant === 'contained'
		? colorVariants[color].divider
		: ''} {wrapperClasses} flex flex-row"
	style={wrapperStyle}
>
	{#if href}
		<a
			bind:this={element}
			on:pointerdown
			on:focus
			on:blur
			on:mouseenter
			on:mouseleave
			on:click={() => {
				loading = true
				dispatch('click', event)
				if (!loadUntilNav) {
					loading = false
				}
			}}
			{href}
			{download}
			class={buttonClass}
			{id}
			{target}
			tabindex={disabled ? -1 : 0}
			{...$$restProps}
			{style}
		>
			{#if loading}
				<Loader2 class={twMerge('animate-spin', iconOnlyPadding[size])} size={lucideIconSize} />
			{:else if startIcon?.icon}
				<svelte:component
					this={startIcon.icon}
					class={twMerge(startIcon?.classes, iconOnlyPadding[size])}
					size={lucideIconSize}
				/>
			{/if}

			{#if !iconOnly}
				<slot />
			{/if}
			{#if endIcon?.icon}
				<svelte:component
					this={endIcon.icon}
					class={twMerge(endIcon?.classes, iconOnlyPadding[size])}
					size={lucideIconSize}
				/>
			{/if}
			{#if shortCut && !shortCut.hide}
				<div class="flex flex-row items-center !text-md opacity-60 gap-0 font-normal">
					{#if shortCut.withoutModifier !== true}{getModifierKey()}{/if}{#if shortCut.Icon}<shortCut.Icon
							class="w-4 h-4"
							size={lucideIconSize}
						/>{:else}{shortCut.key}{/if}
				</div>
			{/if}
		</a>
	{:else}
		<button
			bind:this={element}
			on:pointerdown
			on:click={onClick}
			on:focus
			on:blur
			on:mouseenter
			on:mouseleave
			class={buttonClass}
			{id}
			tabindex={disabled ? -1 : 0}
			{title}
			{...$$restProps}
			disabled={disabled || (loading && !clickableWhileLoading)}
			{style}
		>
			{#if loading}
				<Loader2 class={twMerge('animate-spin', iconOnlyPadding[size])} size={lucideIconSize} />
			{:else if startIcon?.icon}
				<svelte:component
					this={startIcon.icon}
					class={twMerge(startIcon?.classes, iconOnlyPadding[size])}
					size={lucideIconSize}
				/>
			{/if}

			{#if !iconOnly}
				<slot />
			{/if}
			{#if endIcon?.icon}
				<svelte:component
					this={endIcon.icon}
					class={twMerge(endIcon?.classes, iconOnlyPadding[size])}
					size={lucideIconSize}
				/>
			{/if}
			{#if shortCut && !shortCut.hide}
				{@const Icon = shortCut.Icon}
				<div class="flex flex-row items-center !text-md opacity-60 gap-0 font-normal">
					{#if shortCut.withoutModifier !== true}{getModifierKey()}{/if}{#if shortCut.Icon}<Icon
							size={lucideIconSize}
						/>{:else}{shortCut.key}{/if}
				</div>
			{/if}
		</button>
	{/if}

	{#if dropdownItems && dropdownItems.length > 0}
		<div
			class={twMerge(
				buttonClass,
				'rounded-md m-0 p-0 h-auto !w-10',
				variant === 'border' ? 'border-0 border-r border-y ' : 'border-0',
				'rounded-r-md !rounded-l-none'
			)}
		>
			<ButtonDropdown target={portalTarget}>
				<svelte:fragment slot="items">
					{#each computeDropdowns() ?? [] as item}
						<MenuItem on:click={item.onClick} href={item.href}>
							<div
								class={classNames(
									'!text-secondary text-left px-4 py-2 gap-2 cursor-pointer hover:bg-surface-hover !text-xs font-semibold'
								)}
							>
								{#if item.icon}
									<svelte:component this={item.icon} class="w-4 h-4" size={lucideIconSize} />
								{/if}
								{item.label}
							</div>
						</MenuItem>
					{/each}
				</svelte:fragment>
			</ButtonDropdown>
		</div>
	{/if}
</div>
