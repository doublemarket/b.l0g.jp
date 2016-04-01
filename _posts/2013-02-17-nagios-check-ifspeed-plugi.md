---
id: 1409
title: NICのリンクスピードを監視するNagiosスクリプト
date: 2013-02-17T16:30:00+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1409
permalink: /nagios/nagios-check-ifspeed-plugi/
categories:
  - Nagios
---

Nagiosのプラグインには、NICのステータス(up/downとか)をチェックする check_ifstatus がある。しかし、ネゴシエーションなどの関係で、リンクアップはしているがリンクスピードが1Gbps full duplexであってほしいのに10Mbps halfとかになっているということはよくあること。

探してみた感じこれをチェックするためのNagiosプラグインはなさそうだったので、シェルスクリプト書いてみた。本当はcheck_ifstatus(perl)を拡張するのがいいのかもしれないが。

<noscript>
  <pre><code class="language-shell shell">#!/bin/bash

#END STATUS
OK=0
WARNING=1
CRITICAL=2
UNKNOWN=3

# snmpwalk command
SNMPWALK=/usr/local/net-snmp/bin/snmpwalk

# default parameter
RET=${OK}
IP="127.0.0.1"
COMMUNITY="public"
INTERFACE="eth0"
SPEED=""
WARN_FLG="FALSE"
CRIT_FLG="FALSE"

# get parameter
while getopts H:C:i:w:c: OPT
do
    case $OPT in
        "H") IP="$OPTARG" ;;
        "C") COMMUNITY="$OPTARG" ;;
        "i") INTERFACE="$OPTARG" ;;
        "w") WARN_FLG="TRUE" ; WARN_TH="$OPTARG" ;;
        "c") CRIT_FLG="TRUE" ; CRIT_TH="$OPTARG" ;;
    esac
done

# check parameter
if [ ${WARN_FLG} = "FALSE" ] && [ ${CRIT_FLG} = "FALSE" ] ; then
    echo "Parameter Error : -w or -c is required entry"
    exit ${WARNING}
fi

# get speed
INDEX=`${SNMPWALK} -t 10 -v 2c -c ${COMMUNITY} ${IP} ifDescr | grep ${INTERFACE} | sed 's/.*\.//' | sed 's/ .*//'`
SPEED_bps=`${SNMPWALK} -t 10 -v 2c -c casnmp ${IP} ifSpeed.${INDEX} | awk '{print $4}'`
SPEED=`expr ${SPEED_bps} / 1000000`

if [ -z ${SPEED} ] ; then
    echo "Can't get ${INTERFACE} speed"
    exit ${WARNING}
fi

# get result
if [ ${WARN_FLG} = "TRUE" ] && [ ${CRIT_FLG} = "TRUE" ] ; then
    if [ ${SPEED} -lt ${CRIT_TH} ] ; then
        RET=${CRITICAL}
    fi
    if [ ${SPEED} -lt ${WARN_TH} ] && [ ${OFFSET} -ge ${CRIT_TH} ] ; then
        RET=${WARNING}
    fi
fi
if [ ${WARN_FLG} = "TRUE" ] && [ ${CRIT_FLG} = "FALSE" ] ; then
    if [ ${SPEED} -lt ${WARN_TH} ] ; then
        RET=${WARNING}
    fi
fi
if [ ${WARN_FLG} = "FALSE" ] && [ ${CRIT_FLG} = "TRUE" ] ; then
    if [ ${SPEED} -lt ${CRIT_TH} ] ; then
        RET=${CRITICAL}
    fi
fi

# information
echo "${INTERFACE} speed is ${SPEED}Mbps now"

exit ${RET}
</code></pre>
</noscript>



* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
