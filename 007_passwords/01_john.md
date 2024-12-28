locate *2john*

john --format=sha256 hashes_to_crack.txt
john --wordlist=<wordlist_file> --rules <hash_file>
john --incremental <hash_file>