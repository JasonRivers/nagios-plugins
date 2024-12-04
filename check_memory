#!/usr/bin/env bash

STATE_OK=0
STATE_WARNING=1
STATE_CRITICAL=2
STATE_UNKNOWN=3

EXITCODE=$STATE_UNKNOWN

WARNING=85
CRITICAL=90

while getopts H:v:C:w:c:hV:D OPT
do
  case "$OPT" in
    w) WARNING=$OPTARG ;;
    c) CRITICAL=$OPTARG ;;
    h) print_help
       exit "$STATE_UNKNOWN" ;;
    V) print_version
       exit "$STATE_UNKNOWN";;
    D) DEBUG=true ;;
   esac
done

MEMTOTAL=$(cat /proc/meminfo | awk '/^MemTotal/ {print $2}')
MEMAVAILABLE=$(cat /proc/meminfo | awk '/^MemAvailable/ {print $2}')
MEMCACHED=$(cat /proc/meminfo | awk '/^Cached/ {print $2}')

let MEMUSED=$MEMTOTAL-$MEMAVAILABLE
MEM_PERCENT_USED=$(awk "BEGIN { pc=100*${MEMUSED}/${MEMTOTAL}; i=int(pc); print (pc-i<0.5)?i:i+1 }")

if [ $MEM_PERCENT_USED -ge $CRITICAL ]; then
   MESSAGE="MEMORY CRITICAL: ${MEM_PERCENT_USED}% Memory used"
   EXITCODE=$STATE_CRITICAL
elif [ $MEM_PERCENT_USED -ge $WARNING ]; then
   MESSAGE="MEMORY WARNING: ${MEM_PERCENT_USED}% Memory used"
   EXITCODE=$STATE_WARNING
else
   MESSAGE="MEMORY OK: ${MEM_PERCENT_USED}% Memory used"
   EXITCODE=$STATE_OK
fi


PERFDATA="TOTAL=${MEMTOTAL};;;; USED=${MEMUSED};;;; FREE=${MEMAVAILABLE};;;; CACHES=${MEMCACHED};;;;"


#TOTAL=3911392KB;;;; USED=2790452KB;3129113;3520252;; FREE=1120940KB;;;; CACHES=659512KB;;;;

echo "${MESSAGE} | ${PERFDATA}"
exit $EXITCODE