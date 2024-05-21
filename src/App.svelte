<script lang="ts">
    import { createClient } from "@supabase/supabase-js";
    import "./app.css";
    import { Button } from "$lib/components/ui/button";
    import * as Table from "$lib/components/ui/table";
    import { onMount } from "svelte";
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
        return data;
    }

    // Create a function to handle inserts
    function handleInserts(payload) {
        data?.push({ timestamp: payload.new.timestamp });
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
</script>

<main>
    <div class="text-center pt-96">
        <Button on:click={setUnixTime} class="">Log Unix Time</Button>
        <Table.Root
            class="w-96 mx-auto border-r-2 border-l-2 border-t-2 border-b-black border-b-2 mt-4 mb-4"
        >
            <Table.Header>
                <Table.Row>
                    <Table.Head class="text-center">Timestamp</Table.Head>
                </Table.Row>
            </Table.Header>
            <Table.Body>
                {#if data}
                    {#each data as row}
                        <Table.Row>
                            <Table.Cell>{row.timestamp}</Table.Cell>
                        </Table.Row>
                    {/each}
                {:else}
                    <Table.Row>
                        <Table.Cell>Loading...</Table.Cell>
                    </Table.Row>
                {/if}
            </Table.Body>
        </Table.Root>
    </div>
</main>

<style>
</style>
