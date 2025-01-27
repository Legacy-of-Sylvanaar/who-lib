# LibWho-3.0

This documentation is for developers looking to utilize the World of Warcraft /who subsystem. The /who subsystem is a shared resource among all addons using it. By using this library, you will ensure that you do not conflict with other addons using the /who subsystem

This library provides the following:

* An Interface for a information's about an user
  (The 2.0 feature that is not yet available for 3.0 now)
* A much better who interface, with guarantee to be executed & callback

## Usage

```
---Your callback
---@param query string The query text of the returned query result.
---@param results WhoInfo[] Check https://warcraft.wiki.gg/wiki/API_C_FriendList.GetWhoInfo for the document.
local function YourCallback(query, results)
  for i, whoInfo in pairs(results) do
    print(whoInfo.fullName) -- This will print out something as `John-Blackhand`.
  end
end

LibStub('LibWho-3.0'):Who('77-80 c-"Warrior"', YourCallback)

-- Then click the screen in game to trigger the hardware event.
```

## API Documentation

### :Who(query, callback)
#### Args
    query
        string - the search string
    callback
        function(query: string, results: WhoInfo[]) - a callback that will get invoked when the query is done.
    Returns
        nil
Check [WhoInfo](https://warcraft.wiki.gg/wiki/API_C_FriendList.GetWhoInfo) for the detailed fields.
#### Example
Check [here](#usage) to see the example.
#### Remarks
If you're only interested in the information of one player, use :UserInfo() instead. (You can set opts.timeout to 0 if you don't accept cached data.)

### :SetWhoLibDebug(state)
#### Args
    state
        boolean - Enables or disables the debugging
    Returns
        nil
Enable/Disable the debugging messages. Check [Debug](#debug) for details.

## Debug

When debugging is enabled then the chat will be filled with added/returned entries, one for each query.

```
21:01:40 WhoLib: [3] added "n-Lager", queues=0, 0, 1
21:01:40 WhoLib: [3] returned "n-Lager", total=0, queues=0, 0, 0
```
The [3] means Queue 3 = `WHOLIB_QUEUE_SCANNING`, each query will at first be "added" and later "returned", on returned queries the total number of entries will also be printed. The "queues=0, 0, 1" means that 0 queries are in the .`WHOLIB_QUEUE_USER` queue, 0 in .`WHOLIB_QUEUE_QUIET`, and 1 (the added one) in .`WHOLIB_QUEUE_SCANNING`.
For :UserInfo() even more entries will be printed.

### Slash commands

#### `/wholib-debug [on|off]`

Toggles the debugging.

#### `/wholib-debug status [filter]`

Displays the status of the internal variables of WhoLib. You can specify a filter to only display the matched variable.

For example:

```
/wholib-debug status Queue
Queue
  1.
    query: z-"Dornogal" 77-80
    callback: function: 0000025D1...
    queue: 1
  2.
  3.
AllQueuesEmpty function: 0000...
...
```

#### `/wholib-test [filter]`

Runs self tests.

Note the tests contains functions that mostly require the player clicks without doing anything else (however, you can still walk around to feel the world). Due to the throttling of `SendWho` API, the tests can take minutes to complete.
