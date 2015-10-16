# Redis API

## Connection

* connect, open - Connect to a server
* pconnect, popen - Connect to a server (persistent)
* auth - Authenticate to the server
* select - Change the selected database for the current connection
* close - Close the connection
* setOption - Set client option
* getOption - Get client option
* ping - Ping the server
* echo - Echo the given string

## Server

* bgrewriteaof - Asynchronously rewrite the append-only file
* bgsave - Asynchronously save the dataset to disk (in background)
* config - Get or Set the Redis server configuration parameters
* dbSize - Return the number of keys in selected database
* flushAll - Remove all keys from all databases
* flushDB - Remove all keys from the current database
* info - Get information and statistics about the server
* lastSave - Get the timestamp of the last disk save
* resetStat - Reset the stats returned by info method.
* save - Synchronously save the dataset to disk (wait to complete)
* slaveof - Make the server a slave of another instance, or promote it to master
* time - Return the current server time
* slowlog - Access the Redis slowlog entries

## Strings

* append - Append a value to a key
* bitcount - Count set bits in a string
* bitop - Perform bitwise operations between strings
* decr, decrBy - Decrement the value of a key
* get - Get the value of a key
* getBit - Returns the bit value at offset in the string value stored at key
* getRange - Get a substring of the string stored at a key
* getSet - Set the string value of a key and return its old value
* incr, incrBy - Increment the value of a key
* incrByFloat - Increment the float value of a key by the given amount
* mGet, getMultiple - Get the values of all the given keys
* mSet, mSetNX - Set multiple keys to multiple values
* set - Set the string value of a key
* setBit - Sets or clears the bit at offset in the string value stored at key
* setex, psetex - Set the value and expiration of a key
* setnx - Set the value of a key, only if the key does not exist
* setRange - Overwrite part of a string at key starting at the specified offset
* strlen - Get the length of the value stored in a key

## Keys

* del, delete - Delete a key
* dump - Return a serialized version of the value stored at the specified key.
* exists - Determine if a key exists
* expire, setTimeout, pexpire - Set a key's time to live in seconds
* expireAt, pexpireAt - Set the expiration for a key as a UNIX timestamp
* keys, getKeys - Find all keys matching the given pattern
* scan - Scan for keys in the keyspace (Redis >= 2.8.0)
* migrate - Atomically transfer a key from a Redis instance to another one
* move - Move a key to another database
* object - Inspect the internals of Redis objects
* persist - Remove the expiration from a key
* randomKey - Return a random key from the keyspace
* rename, renameKey - Rename a key
* renameNx - Rename a key, only if the new key does not exist
* type - Determine the type stored at key
* sort - Sort the elements in a list, set or sorted set
* ttl, pttl - Get the time to live for a key
* restore - Create a key using the provided serialized value, previously obtained with dump.

## Hashes

* hDel - Delete one or more hash fields
* hExists - Determine if a hash field exists
* hGet - Get the value of a hash field
* hGetAll - Get all the fields and values in a hash
* hIncrBy - Increment the integer value of a hash field by the given number
* hIncrByFloat - Increment the float value of a hash field by the given amount
* hKeys - Get all the fields in a hash
* hLen - Get the number of fields in a hash
* hMGet - Get the values of all the given hash fields
* hMSet - Set multiple hash fields to multiple values
* hSet - Set the string value of a hash field
* hSetNx - Set the value of a hash field, only if the field does not exist
* hVals - Get all the values in a hash
* hScan - Scan a hash key for members

## Lists

* blPop, brPop - Remove and get the first/last element in a list
* brpoplpush - Pop a value from a list, push it to another list and return it
* lIndex, lGet - Get an element from a list by its index
* lInsert - Insert an element before or after another element in a list
* lLen, lSize - Get the length/size of a list
* lPop - Remove and get the first element in a list
* lPush - Prepend one or multiple values to a list
* lPushx - Prepend a value to a list, only if the list exists
* lRange, lGetRange - Get a range of elements from a list
* lRem, lRemove - Remove elements from a list
* lSet - Set the value of an element in a list by its index
* lTrim, listTrim - Trim a list to the specified range
* rPop - Remove and get the last element in a list
* rpoplpush - Remove the last element in a list, append it to another list and return it (redis >= 1.1)
* rPush - Append one or multiple values to a list
* rPushx - Append a value to a list, only if the list exists

## Sets

* sAdd - Add one or more members to a set
* sCard, sSize - Get the number of members in a set
* sDiff - Subtract multiple sets
* sDiffStore - Subtract multiple sets and store the resulting set in a key
* sInter - Intersect multiple sets
* sInterStore - Intersect multiple sets and store the resulting set in a key
* sIsMember, sContains - Determine if a given value is a member of a set
* sMembers, sGetMembers - Get all the members in a set
* sMove - Move a member from one set to another
* sPop - Remove and return a random member from a set
* sRandMember - Get one or multiple random members from a set
* sRem, sRemove - Remove one or more members from a set
* sUnion - Add multiple sets
* sUnionStore - Add multiple sets and store the resulting set in a key
* sScan - Scan a set for members

## Sorted sets

* zAdd - Add one or more members to a sorted set or update its score if it already exists
* zCard, zSize - Get the number of members in a sorted set
* zCount - Count the members in a sorted set with scores within the given values
* zIncrBy - Increment the score of a member in a sorted set
* zInter - Intersect multiple sorted sets and store the resulting sorted set in a new key
* zRange - Return a range of members in a sorted set, by index
* zRangeByScore, zRevRangeByScore - Return a range of members in a sorted set, by score
* zRangeByLex - Return a lexigraphical range from members that share the same score
* zRank, zRevRank - Determine the index of a member in a sorted set
* zRem, zDelete - Remove one or more members from a sorted set
* zRemRangeByRank, zDeleteRangeByRank - Remove all members in a sorted set within the given indexes
* zRemRangeByScore, zDeleteRangeByScore - Remove all members in a sorted set within the given scores
* zRevRange - Return a range of members in a sorted set, by index, with scores ordered from high to low
* zScore - Get the score associated with the given member in a sorted set
* zUnion - Add multiple sorted sets and store the resulting sorted set in a new key
* zScan - Scan a sorted set for members

## Pub/sub

* psubscribe - Subscribe to channels by pattern
* publish - Post a message to a channel
* subscribe - Subscribe to channels
* pubsub - Introspection into the pub/sub subsystem

## Scripting

* eval - Evaluate a LUA script serverside
* evalSha - Evaluate a LUA script serverside, from the SHA1 hash of the script instead of the script itself
* script - Execute the Redis SCRIPT command to perform various operations on the scripting subsystem
* getLastError - The last error message (if any)
* clearLastError - Clear the last error message
* _prefix - A utility method to prefix the value with the prefix setting for phpredis
* _unserialize - A utility method to unserialize data with whatever serializer is set up
* _serialize - A utility method to serialize data with whatever serializer is set up
