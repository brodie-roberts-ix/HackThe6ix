# Files for presentation "Concurrent Optimization Methods Using Go"
Written and presented by Brodie Roberts on behalf of Index Excahnge.

Presented at the Hack The 6ix workshop at UWaterloo on June 5, 2018.

# Moved to [Index Exchange's official github](https://github.com/indexexchange/GoOptimizationMethods)

## Setup

```
# Generate 10M data sample
mkdir -p data/10M
time go run generator.go -n 100000 > data/10M/server_1.log
time for i in {2..100}; do cp data/10M/server_1.log data/10M/server_$i.log; done

# Generate 300M data sample (>20 minutes)
mkdir -p data/300M
time for i in {1..300}; do go run generator.go -r -n 1000000 > data/300M/server_$i.log; done
```


## Baseline: Bash
```
# Lower Bound
time cat data/10M/* | wc -l

# Upper Bound
time cat data/10M/* | cut -f2 | grep -v "^0$" | sort -n | uniq -c | wc -l
time cat data/10M/* | grep -P "^.*\t0\t" | cut -f3,4,5 | sort -n | uniq -c | wc -l
```


## Version 1: Go
```
# Run basic program
go run HT6_1.go data/10M

# Strip-down testing of V1
## Add channel buffering
go run HT6_1.go data/10M
```


## Version 2: Channel Buffering
```
# Run V2
go run HT6_2.go data/10M

# Add channel batching to V1
go run HT6_2.go data/10M
```


## Version 3: Channel Batching
```
# Run V3
go run HT6_3.go -b 64 data/10M

# Strip-down testing of V3
## Add concurrency
go run HT6_3.go -b 64 data/10M
```


## Version 4: Parse Concurrency
```
# Run V4
go run HT6_4.go -b 64 -c 4 data/10M

# Strip-down testing of V4
## Add MakeKey
go run HT6_4.go -b 64 -c 4 data/10M
```


## Version 5: Optimise Sequential Section
```
## Run V5
go run HT6_5.go -b 64 -c 16 data/10M
```


### Slow Commands
```
# Versions of the above commands on the large sample data (several >20 minutes)
go run HT6_1.go data/300M
go run HT6_2.go data/300M
go run HT6_3.go -b 64 data/300M
go run HT6_4.go -b 64 -c 4 data/300M
go run HT6_5.go -b 64 -c 16 data/300M
```

