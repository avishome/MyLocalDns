import socket
import dnslib
from dnslib import  RR, QTYPE, A, PTR
DataFromFile = {
  "google.com" : [{"type":"A","ip":"123.123.123.123", "TTL":60},
                  {"type":"NS","ip":"123.123.123.321", "TTL":60},
                  {"type":"MX","ip":"123.123.123.8", "TTL":60}],
  "walla.co.il" : [{"type":"A","ip":"123.123.123.123", "TTL":60}],
  "1.0.0.127.in-addr.arpa" : [{"type":"PTR","ip":"your.best.dns.server", "TTL":60}]
}
TTL = 60
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_socket.bind(('', 53))
for i in range(3):
    message, address = server_socket.recvfrom(1024)
    Dnsmessage = dnslib.DNSRecord.parse(message)
    qname = str(Dnsmessage.q.qname).rsplit('.', 1)[0]
    qtype = QTYPE[Dnsmessage.q.qtype]
    print("new query for \""+str(qname)+"\" as type "+str(qtype))
    response = Dnsmessage.reply()
    if str(qname) not in DataFromFile:
        print("uot")
        continue
    if qtype == "PTR" and str(qname) in DataFromFile:
        response.add_answer(RR(rname=qname, rtype=QTYPE.PTR,
                        rclass=1, ttl=DataFromFile[qname][0]["TTL"],
                        rdata=PTR(DataFromFile[qname][0]["ip"])))
    else:
        [response.add_answer(
            RR(qname,eval("QTYPE."+x["type"]),rdata=A(x["ip"]),ttl=x["TTL"])) 
            for x in DataFromFile[qname] if x["type"] == qtype
            ]  
    Bresponse = bytes(response.pack())
    server_socket.sendto(Bresponse, address)
