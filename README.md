---
soap-server
---

Soap server, using pure javascript for node.js.

## Simple example

	var soap = require('soap-server');
	
	function MyTestService(){
	}
	MyTestService.prototype.test1 = function(myArg1, myArg2){
		return myArg1 + myArg2;
	};

	var soapServer = new soap.SoapServer();
	var soapService = soapServer.addService('testService', new MyTestService());
		
	soapServer.listen(1337, '127.0.0.1');
	
The WSDL at http://127.0.0.1:1337/testService?wsdl would be:

	<?xml version="1.0"?>
	<definitions xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" xmlns:wsp="http://www.w3.org/ns/ws-policy" xmlns:wsp1_2="http://schemas.xmlsoap.org/ws/2004/09/policy" xmlns:tns="http://server.soap.com/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:wsam="http://www.w3.org/2007/05/addressing/metadata" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns="http://schemas.xmlsoap.org/wsdl/" targetNamespace="http://server.soap.com/" name="testServiceService">
		<types>
			<xsd:schema version="1.0" targetNamespace="http://server.soap.com/">
				<xsd:element name="MyObject" type="tns:MyObject"/>
				<xsd:complexType name="MyObject">
					<xsd:sequence>
						<xsd:element name="concated" type="xsd:string" minOccurs="0"/>
						<xsd:element name="incremented" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
				<xsd:element name="MyTestObject" type="tns:MyTestObject"/>
				<xsd:complexType name="MyTestObject">
					<xsd:sequence>
						<xsd:element name="strArg" type="xsd:string" minOccurs="0"/>
						<xsd:element name="intArg" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
			</xsd:schema>
		</types>
		<message name="test1">
			<part name="myArg1" element="xsd:string"/>
			<part name="myArg2" element="xsd:string"/>
		</message>
		<message name="test1Response">
			<part name="return" element="xsd:string"/>
		</message>
		<portType name="testService">
			<operation name="test1">
				<input wsam:Action="test1" message="tns:test1"/>
				<output wsam:Action="http://server.soap.com/testService/test1Response" message="tns:test1Response"/>
			</operation>
		</portType>
		<binding name="testServicePortBinding" type="tns:testService">
			<soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="rpc"/>
			<operation name="test1">
				<soap:operation soapAction="test1"/>
				<input name="test1">
					<soap:body use="literal"/>
				</input>
				<output name="test1Response">
					<soap:body use="literal"/>
				</output>
			</operation>
		</binding>
		<service name="testServiceService">
			<port name="testServicePort" binding="tns:testServicePortBinding">
				<soap:address location="http://127.0.0.1:1337/testService"/>
			</port>
		</service>
	</definitions>

## Forcing arguments and returned types	
	
	function MyTestService(){
	}
	MyTestService.prototype.test2 = function(myArg1, myArg2){
		return myArg1 + myArg2;
	};
	
	var soapServer = new soap.SoapServer();
	var soapService = soapServer.addService('testService', new MyTestService());
	
	var test2operation = soapService.getOperation('test2');
	test2operation.setOutputType('number');
	test2operation.setInputType('myArg1', {type: 'number'});
	test2operation.setInputType('myArg2', {type: 'number'});
	
	soapServer.listen(1337, '127.0.0.1');
	    
The WSDL at http://127.0.0.1:1337/testService?wsdl would be:
	    	
	<?xml version="1.0"?>
	<definitions xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" xmlns:wsp="http://www.w3.org/ns/ws-policy" xmlns:wsp1_2="http://schemas.xmlsoap.org/ws/2004/09/policy" xmlns:tns="http://server.soap.com/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:wsam="http://www.w3.org/2007/05/addressing/metadata" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns="http://schemas.xmlsoap.org/wsdl/" targetNamespace="http://server.soap.com/" name="testServiceService">
		<types>
			<xsd:schema version="1.0" targetNamespace="http://server.soap.com/">
				<xsd:element name="MyObject" type="tns:MyObject"/>
				<xsd:complexType name="MyObject">
					<xsd:sequence>
						<xsd:element name="concated" type="xsd:string" minOccurs="0"/>
						<xsd:element name="incremented" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
				<xsd:element name="MyTestObject" type="tns:MyTestObject"/>
				<xsd:complexType name="MyTestObject">
					<xsd:sequence>
						<xsd:element name="strArg" type="xsd:string" minOccurs="0"/>
						<xsd:element name="intArg" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
			</xsd:schema>
		</types>
		<message name="test2">
			<part name="myArg1" element="xsd:int"/>
			<part name="myArg2" element="xsd:int"/>
		</message>
		<message name="test2Response">
			<part name="return" element="xsd:int"/>
		</message>
		<portType name="testService">
			<operation name="test2">
				<input wsam:Action="test2" message="tns:test2"/>
				<output wsam:Action="http://server.soap.com/testService/test2Response" message="tns:test2Response"/>
			</operation>
		</portType>
		<binding name="testServicePortBinding" type="tns:testService">
			<soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="rpc"/>
			<operation name="test2">
				<soap:operation soapAction="test2"/>
				<input name="test2">
					<soap:body use="literal"/>
				</input>
				<output name="test2Response">
					<soap:body use="literal"/>
				</output>
			</operation>
		</binding>
		<service name="testServiceService">
			<port name="testServicePort" binding="tns:testServicePortBinding">
				<soap:address location="http://127.0.0.1:1337/testService"/>
			</port>
		</service>
	</definitions>
	    
## Object type response

	function MyObject(){
	}
	MyObject.prototype.concated = '';
	MyObject.prototype.incremented = 0;
	
	function MyTestService(){
	}
	MyTestService.prototype.test3 = function(strArg, intArg){
		var ret = new MyObject();
		ret.concated = strArg + '[' + intArg + ']';
		ret.incremented = intArg + 1;
		return ret;
	};
	
	var soapServer = new soap.SoapServer();
	var soapService = soapServer.addService('testService', new MyTestService());
	
	var test3operation = soapService.getOperation('test3');
	test3operation.setOutputType(MyObject, 'MyObject');
	test3operation.setInputType('intArg', {type: 'number'});
	
	soapServer.listen(1337, '127.0.0.1');
	
The WSDL at http://127.0.0.1:1337/testService?wsdl would be:

	<?xml version="1.0"?>
	<definitions xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" xmlns:wsp="http://www.w3.org/ns/ws-policy" xmlns:wsp1_2="http://schemas.xmlsoap.org/ws/2004/09/policy" xmlns:tns="http://server.soap.com/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:wsam="http://www.w3.org/2007/05/addressing/metadata" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns="http://schemas.xmlsoap.org/wsdl/" targetNamespace="http://server.soap.com/" name="testServiceService">
		<types>
			<xsd:schema version="1.0" targetNamespace="http://server.soap.com/">
				<xsd:element name="MyObject" type="tns:MyObject"/>
				<xsd:complexType name="MyObject">
					<xsd:sequence>
						<xsd:element name="concated" type="xsd:string" minOccurs="0"/>
						<xsd:element name="incremented" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
				<xsd:element name="MyTestObject" type="tns:MyTestObject"/>
				<xsd:complexType name="MyTestObject">
					<xsd:sequence>
						<xsd:element name="strArg" type="xsd:string" minOccurs="0"/>
						<xsd:element name="intArg" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
			</xsd:schema>
		</types>
		<message name="test2Response">
			<part name="return" element="xsd:int"/>
		</message>
		<message name="test3">
			<part name="strArg" element="xsd:string"/>
			<part name="intArg" element="xsd:int"/>
		</message>
		<message name="test3Response">
			<part name="return" element="tns:MyObject"/>
		</messag
		<portType name="testService">
			<operation name="test3">
				<input wsam:Action="test3" message="tns:test3"/>
				<output wsam:Action="http://server.soap.com/testService/test3Response" message="tns:test3Response"/>
			</operation>
		</portType>
		<binding name="testServicePortBinding" type="tns:testService">
			<soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="rpc"/>
			<operation name="test3">
				<soap:operation soapAction="test3"/>
				<input name="test3">
					<soap:body use="literal"/>
				</input>
				<output name="test3Response">
					<soap:body use="literal"/>
				</output>
			</operation>
		</binding>
		<service name="testServiceService">
			<port name="testServicePort" binding="tns:testServicePortBinding">
				<soap:address location="http://127.0.0.1:1337/testService"/>
			</port>
		</service>
	</definitions>

## Object type request

	function MyTestObject(){
	}
	MyTestObject.prototype.strArg = '';
	MyTestObject.prototype.intArg = 0;
	
	function MyTestService(){
	}
	MyTestService.prototype.test4 = function(myTestObjectInstance){
		return myTestObjectInstance.strArg + '[' + myTestObjectInstance.intArg + ']';
	};
	
	var soapServer = new soap.SoapServer();
	var soapService = soapServer.addService('testService', new MyTestService());
	
	var test4operation = soapService.getOperation('test4');
	test4operation.setInputType('myTestObjectInstance', MyTestObject);
	
	
	soapServer.listen(1337, '127.0.0.1');
	    
The WSDL at http://127.0.0.1:1337/testService?wsdl would be:

	<?xml version="1.0"?>
	<definitions xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" xmlns:wsp="http://www.w3.org/ns/ws-policy" xmlns:wsp1_2="http://schemas.xmlsoap.org/ws/2004/09/policy" xmlns:tns="http://server.soap.com/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:wsam="http://www.w3.org/2007/05/addressing/metadata" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns="http://schemas.xmlsoap.org/wsdl/" targetNamespace="http://server.soap.com/" name="testServiceService">
		<types>
			<xsd:schema version="1.0" targetNamespace="http://server.soap.com/">
				<xsd:element name="MyObject" type="tns:MyObject"/>
				<xsd:complexType name="MyObject">
					<xsd:sequence>
						<xsd:element name="concated" type="xsd:string" minOccurs="0"/>
						<xsd:element name="incremented" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
				<xsd:element name="MyTestObject" type="tns:MyTestObject"/>
				<xsd:complexType name="MyTestObject">
					<xsd:sequence>
						<xsd:element name="strArg" type="xsd:string" minOccurs="0"/>
						<xsd:element name="intArg" type="xsd:int" minOccurs="0"/>
					</xsd:sequence>
				</xsd:complexType>
			</xsd:schema>
		</types>
		<message name="test4">
			<part name="myTestObjectInstance" element="tns:MyTestObject"/>
		</message>
		<message name="test4Response">
			<part name="return" element="xsd:string"/>
		</message>
		<portType name="testService">
			<operation name="test4">
				<input wsam:Action="test4" message="tns:test4"/>
				<output wsam:Action="http://server.soap.com/testService/test4Response" message="tns:test4Response"/>
			</operation>
		</portType>
		<binding name="testServicePortBinding" type="tns:testService">
			<soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="rpc"/>
			<operation name="test4">
				<soap:operation soapAction="test4"/>
				<input name="test4">
					<soap:body use="literal"/>
				</input>
				<output name="test4Response">
					<soap:body use="literal"/>
				</output>
			</operation>
		</binding>
		<service name="testServiceService">
			<port name="testServicePort" binding="tns:testServicePortBinding">
				<soap:address location="http://127.0.0.1:1337/testService"/>
			</port>
		</service>
	</definitions>
