<h2>Get started with AWS Client VPN</h2>




```
git clone git@github.com:OpenVPN/easy-rsa.git
cd easy-rsa/easyrsa3
ll
./easyrsa init-pki
./easyrsa build-ca nopass
./easyrsa build-server-full technonext-client-vpn.com nopass
./easyrsa build-client-full nasir.technonext-client-vpn.com nopass\n
mkdir acm
cp pki/ca.crt acm
cp pki/issued/technonext-client-vpn.com.crt acm
cp pki/issued/nasir.technonext-client-vpn.com.crt acm
cp pki/private/technonext-client-vpn.com.key acm
cp pki/private/nasir.technonext-client-vpn.com.key acm
cd acm
```
```
aws acm import-certificate --certificate fileb://technonext-client-vpn.com.crt --private-key fileb://technonext-client-vpn.com.key --certificate-chain fileb://ca.crt --region us-east-1

naws acm import-certificate --certificate fileb://nasir.technonext-client-vpn.com.crt --private-key fileb://nasir.technonext-client-vpn.com.key --certificate-chain fileb://ca.crt --region us-east-1
```
[Ref](https://prasaddomala.com/2020/04/02/aws-client-vpn-setup-private-access-across-aws-accounts-and-vpcs/)


[Download vpn client](https://docs.aws.amazon.com/vpn/latest/clientvpn-user/client-vpn-connect-linux-install.html)




[References 11](https://www.youtube.com/watch?v=8En5Dd-dHFw)

[References 12](https://www.youtube.com/watch?v=Bv70DoHDDCY)



[References1](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/cvpn-getting-started.html)

[References2](https://www.youtube.com/watch?v=Bv70DoHDDCY)


[R3](https://www.youtube.com/watch?v=qluch6r528s)