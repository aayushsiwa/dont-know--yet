<script lang="ts">
    import { createClient } from "@supabase/supabase-js";
    import "./app.css";
    import { Button } from "$lib/components/ui/button";
    import * as Table from "$lib/components/ui/table";
    import { onMount } from "svelte";
    import { ModeWatcher } from "mode-watcher";
    import moon from "../src/assets/moon.svg";
    import sun from "../src/assets/sun.svg";
    // import Sun from "lucide-svelte/icons/sun";
    // import Moon from "lucide-svelte/icons/moon";
    import { toggleMode } from "mode-watcher";
    // console.log(import.meta.env.ANON_KEY)
    // Create a single supabase client for interacting with your database
    const supUrl = import.meta.env.VITE_SUPABASE_PUBLIC_URL;
    const anonKey = import.meta.env.VITE_ANON_KEY;
    const supabase = createClient(supUrl, anonKey);
    // console.log(supUrl,anonKey)

    let data: Array<{ timestamp: any }> | null;

    async function setUnixTime() {
        console.log(Date.now());
        const { error } = await supabase
            .from("UnixLog")
            .insert({ timestamp: Date.now() });
    }

    async function getData() {
        const { data, error } = await supabase
            .from("UnixLog")
            .select("timestamp");
        data?.reverse();
        return data;
    }

    // Create a function to handle inserts
    function handleInserts(payload: { new: { timestamp: any; }; }) {
        data?.reverse();
        data?.push({ timestamp: payload.new.timestamp });
        data?.reverse();
        data = data;
        console.log("Change received!", payload.new.timestamp);
    }

    onMount(async () => {
        // Listen to inserts
        supabase
            .channel("supabase_realtime")
            .on(
                "postgres_changes",
                { event: "INSERT", schema: "public", table: "UnixLog" },
                handleInserts
            )
            .subscribe();
        getData().then((database_return) => (data = database_return));
    });

    var ph = [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12];
</script>

<ModeWatcher />
<slot />
<main class="h-screen overflow-hidden">
    <div class="text-center pt-28">
        <Button on:click={toggleMode} variant="outline" size="icon" class="mb-2 w-96">
            <img
                src={moon}
                alt=""
                class="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0"
            />
            <img
                src={sun}
                alt=""
                class="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100"
            />
            <span class="sr-only">Toggle theme</span>
        </Button>
        <br>
        <Button on:click={setUnixTime} class="w-96 h-14 font-mono bg-slate-700"
            >Log Unix Time</Button
        >
        <Table.Root
            class="w-96 mx-auto border-r-2 border-l-2 border-t-2 border-b-slate-700 border-b-2 mt-4 mb-4"
        >
            <Table.Header class="sticky top-0 z-10 border-b-2 border-slate-700">
                <Table.Row>
                    <Table.Head class="text-center font-bold font font-mono"
                        >Timestamp</Table.Head
                    >
                </Table.Row>
            </Table.Header>
            <Table.Body class="overflow-y-auto block h-[65vh]">
                {#if data}
                    {#each data as row}
                        <Table.Row
                            class="w-full flex justify-center border-b-2 border-gray-400 font-medium font-mono"
                        >
                            <Table.Cell>{row.timestamp}</Table.Cell>
                        </Table.Row>
                    {/each}
                {:else}
                    <div class="relative">
                        <p class="z-100 absolute w-full text-center py-[30vh]">
                            Loading...
                        </p>
                        {#each ph as p}
                            <Table.Row
                                class="w-full flex justify-center border-b-2 border-gray-400 font-medium font-mono blur-sm"
                            >
                                <Table.Cell>1716311802010</Table.Cell>
                            </Table.Row>
                        {/each}
                    </div>
                {/if}
            </Table.Body>
        </Table.Root>
    </div>
</main>

<style>
</style>
