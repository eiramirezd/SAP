SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO

ALTER proc [dbo].[SBO_SP_TransactionNotification] 

@object_type nvarchar(20), 				-- SBO Object Type
@transaction_type nchar(1),			-- [A]dd, [U]pdate, [D]elete, [C]ancel, C[L]ose
@num_of_cols_in_key int,
@list_of_key_cols_tab_del nvarchar(255),
@list_of_cols_val_tab_del nvarchar(255)

AS

begin

-- Return values
declare @error  int				-- Result (0 for no error)
declare @error_message nvarchar (200) 		-- Error string to be displayed
select @error = 0
select @error_message = N'Ok'

--------------------------------------------------------------------------------------------------------------------------------

--	ADD	YOUR	CODE	HERE

declare @CardType varchar(1)
declare @DataSource varchar(1)
declare @LicTradNum varchar(32)
declare @Notes varchar(100)
declare @Indicator varchar(2)
declare @DocSubType varchar(2)
declare @BeginStr varchar(20)
declare @FolioNum int
declare @Contador int


-----------
declare @GiroRecep varchar(40)
declare @CodigoImpuesto varchar(8)
declare @razonRef varchar(254)
declare @fechaRef varchar(10)
declare @dte int
declare @comen varchar (254)
declare @FolioNum1 int
declare @codRef int
-------------------------------------

declare @direccionFactura varchar (100), @direccionDespacho varchar(100)
declare @ciudadFactura varchar(100), @ciudadDespacho varchar(100)
declare @comunaFactura varchar(100), @comunaDespacho varchar(100)
declare @pedidoECOM varchar(50)

---------------sacar-----------------------------------------------------------------------------------------------------------------
if (@object_type = '2' and @transaction_type in ('A','U'))
begin
	select @LicTradNum = T0.LicTradNum from OCRD T0 where T0.CardCode = @list_of_cols_val_tab_del
	if (dbo.BES_Validar_RUT(@LicTradNum) <> 0)
	begin
		set @error = -1
		set @error_message = N'RUT Invalido para socio de negocios.'
		goto MensajeError
	end
	
	select @Contador = (select count('A') from CRD1 T1 where T1.CardCode = @list_of_cols_val_tab_del and T1.AdresType = 'B')
	
	If (@Contador = 0)
	begin
		set @error = -1
		set @error_message = N'Debe ingresar direccion de Factura'
		goto MensajeError
	end
	
	select @Contador = (select count('A') from CRD1 T1 where T1.CardCode = @list_of_cols_val_tab_del and T1.AdresType = 'S')
	
	If (@Contador = 0)
	begin
		set @error = -1
		set @error_message = N'Debe usar direccion de Despacho'
		goto MensajeError
	end
end

-----------------------------sacar---------------------------------------------------------------------------------------------------

if (@object_type in ('13','14','15') and @transaction_type = 'A')
begin

select @DataSource = 
	case @Object_Type 
		when '13' then (select T0.DataSource from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		when '14' then (select T0.DataSource from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		when '15' then (select T0.DataSource from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
	end
	
	IF @DataSource <> 'O' BEGIN	
			
		select @DocSubType = 
			case @Object_Type 
				when '13' then (select T0.DocSubType from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
				when '14' then (select T0.DocSubType from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
				when '15' then (select T0.DocSubType from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
			end 
					
		select @LicTradNum = 
			case @Object_Type 
				when '13' then (select isnull(T0.LicTradNum,'') from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
				when '14' then (select isnull(T0.LicTradNum,'') from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
				when '15' then (select isnull(T0.LicTradNum,'') from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
			end 
		select @Indicator = 
			case @Object_Type 
				when '13' then (select isnull(T0.Indicator,'') from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
				when '14' then (select isnull(T0.Indicator,'') from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
				when '15' then (select isnull(T0.Indicator,'') from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
			end 

		select @Notes = 
			case @Object_Type 
				when '13' then (select isnull(T2.Notes,'') from OINV T0 inner join OCRD T2 on T2.CardCode = T0.CardCode
							where T0.DocEntry = @list_of_cols_val_tab_del )
				when '14' then (select isnull(T2.Notes,'') from ORIN T0 inner join OCRD T2 on T2.CardCode = T0.CardCode
							where T0.DocEntry = @list_of_cols_val_tab_del )
				when '15' then (select isnull(T2.Notes,'') from ODLN T0 inner join OCRD T2 on T2.CardCode = T0.CardCode
							where T0.DocEntry = @list_of_cols_val_tab_del )
			end 
		select @BeginStr = 
			case @Object_Type
				when '13' then  isnull((select isnull(S.BeginStr,'') from NNM1 S where S.Series = (select T0.Series from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )),'')
				else '' end
				
		select  @direccionFactura = case @object_type
		 when '13' then (select T1.Street from OINV T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		 when '14' then (select T1.Street from ORIN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		 when '15' then (select T1.Street from ODLN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		 end 

		select  @ciudadFactura = case @object_type
		when '13' then (select T1.City from OINV T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		when '14' then (select T1.City from ORIN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		when '15' then (select T1.City from ODLN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		end

		select  @comunaFactura = case @object_type
		when '13' then  (select T1.Block from OINV T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		when '14' then  (select T1.Block from ORIN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		when '15' then  (select T1.Block from ODLN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.PayToCode and T1.AdresType = 'B' )
		end

		select @direccionDespacho = case @object_type
		when '13' then (select T1.Street from OINV T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		when '14' then (select T1.Street from ORIN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		when '15' then (select T1.Street from ODLN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		end

		select  @ciudadDespacho = case @object_type 
		when '13' then (select T1.City from OINV T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		when '14' then (select T1.City from ORIN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		when '15' then (select T1.City from ODLN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		end

		select  @comunaDespacho = case @object_type
		when '13' then (select T1.Block from OINV T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		when '14' then (select T1.Block from ORIN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		when '15' then (select T1.Block from ODLN T0 inner join CRD1 T1 on T1.CardCode = T0.CardCode where T0.DocEntry = @list_of_cols_val_tab_del and T1.[Address] = T0.ShipToCode and T1.AdresType = 'S' )
		end

		select  @razonRef = case @object_type
		when '13' then (select isnull(T0.U_RazonRef,'') from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		when '14' then (select isnull(T0.U_RazonRef,'') from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		--when '15' then (select isnull(T0.Indicator,'') from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		end
		
		
		select  @codRef = case @object_type
		--when '13' then (select isnull(T0.U_RazonRef,'') from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		when '14' then (select isnull(T0.U_CodRef,'') from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		--when '15' then (select isnull(T0.Indicator,'') from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		end

		


		select  @fechaRef = case @object_type
		when '13' then (select isnull(T0.U_FchRef,'') from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		when '14' then (select isnull(T0.U_FchRef,'') from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		--when '15' then (select isnull(T0.Indicator,'') from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		end

		select  @FolioNum1 = case @object_type
		when '13' then (select isnull(T0.U_FolioRef,'') from OINV T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		when '14' then (select isnull(T0.U_FolioRef,'') from ORIN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		--when '15' then (select isnull(T0.Indicator,'') from ODLN T0 where T0.DocEntry = @list_of_cols_val_tab_del )
		end



		/*if (@object_type = '13' and @DocSubType = '--' )
		begin
			if (@Indicator  in ('56','99') and @razonRef is null)
			begin
				set @error = -1
				set @error_message = N'Debe indicar la Razon de la Referencia'
				goto MensajeError
			end
		end*/

		/*if (@object_type = '13' and @DocSubType = '--' )
		begin
			if (@Indicator not in ('33','99'))
			begin
				set @error = -1
				set @error_message = N'El indicador debe ser 33 o 99'
				goto MensajeError
			end
		end*/

		if (@object_type = '13' and @DocSubType = 'DN' )
		begin
			if (@Indicator not in ('56','99'))
			begin
				set @error = -1
				set @error_message = N'El indicador debe ser 56 o 99'
				goto MensajeError
			end
		end

		if (@object_type = '13' and @DocSubType = 'IE' )
		begin
			if (@Indicator not in ('34','99'))
			begin
				set @error = -1
				set @error_message = N'El indicador debe ser 34 o 99'
				goto MensajeError
			end
			
			set @CodigoImpuesto = 'IVA_EXE'
			
			select @Contador = count('A') from INV1 T1 where T1.DocEntry = @list_of_cols_val_tab_del and T1.TaxCode <> @CodigoImpuesto
			
			if (@Contador > 0)
			begin
				set @error = -1
				set @error_message = N'El indicador de impuesto debe ser IVA_EXE.'
				goto MensajeError
			end
		end

		/*if (@object_type = '14' and @DocSubType = '--' )
		begin
			if (@Indicator not in ('61','99'))
			begin
				set @error = -1
				set @error_message = N'El indicador debe ser 61 o 99'
				goto MensajeError
			end
		end*/

		if (@object_type = '14' and @DocSubType = '--' )
		begin
			if (@Indicator  in ('61','99') and (len(@razonRef)= 0) and @codRef != 1)
			begin
				set @error = -1
				set @error_message = N'Debe indicar la razon de la referencia'
			end
		end

		/*if (@object_type = '14' and @DocSubType = '--' )
		begin
			if (@Indicator  in ('61','99') and (len(@FolioNum1)= 0))
			begin
				set @error = -1
				set @error_message = N'Debe indicar el folio de la referencia'
				goto MensajeError
			end
		end

		if (@object_type = '14' and @DocSubType = '--' )
		begin
			if (@Indicator  in ('61','99') and (len(@fechaRef)= 0))
			begin
				set @error = -1
				set @error_message = N'Debe indicar la fecha de la referencia'
				goto MensajeError
			end
		end

		if (@object_type = '14' and @DocSubType = '--' )
		begin
			if (@Indicator  in ('61','99') and (len(@dte)= 0))
			begin
				set @error = -1
				set @error_message = N'Debe indicar el tipo de documento de referencia'
				goto MensajeError
			end
		end*/

		if (@object_type = '13' and @DocSubType = 'DN' )
		begin
			if (@Indicator  in ('56','99') and (len(@razonRef)= 0))
			begin
				set @error = -1
				set @error_message = N'Debe indicar la razon de la referencia'
				goto MensajeError
			end
		end

		if (@object_type = '13' and @DocSubType = 'DN' )
		begin
			if (@Indicator  in ('56','99') and (len(@FolioNum1)= 0))
			begin
				set @error = -1
				set @error_message = N'Debe indicar el folio de la referencia'
				goto MensajeError
			end
		end

		if (@object_type = '13' and @DocSubType = 'DN' )
		begin
			if (@Indicator  in ('56','99') and (len(@fechaRef)= 0) )
			begin
				set @error = -1
				set @error_message = N'Debe indicar la fecha de la referencia'
				goto MensajeError
			end
		end

		if (@object_type = '13' and @DocSubType = 'DN' )
		begin
			if (@Indicator  in ('56','99') and (len(@dte)= 0))
			begin
				set @error = -1
				set @error_message = N'Debe indicar el tipo de documento de referencia'
				goto MensajeError
			end
		end

		if (@object_type = '15' and @DocSubType = '--' )
		begin
			if (@Indicator not in ('52','99'))
			begin
				set @error = -1
				set @error_message = N'El indicador debe ser 52 o 99'
				goto MensajeError
			end
		end

		if(len(@Notes)= 0)
		begin
			set @error = -1
			set @error_message = N'Falta giro de socio de negocios'
			goto MensajeError
		end
			
		/*if (dbo.BES_Validar_RUT(@LicTradNum) <> 0)
		begin
			set @error = -1
			set @error_message = N'RUT Invalido para socio de negocios.'
			goto MensajeError
		end*/

		if (@direccionFactura is null or len(@direccionFactura) = 0)
		begin
			set @error = -1
			set @error_message = N'No existe direccion de factura.'
			goto MensajeError
		end

		if (@direccionDespacho is null or len(@direccionDespacho) = 0)
		begin
			set @error = -1
			set @error_message = N'No existe direccion de despacho.'
			goto MensajeError
		end

		if (@comunaFactura is null or len(@comunaFactura) = 0)
		begin
			set @error = -1
			set @error_message = N'No existe comuna de factura.'
			goto MensajeError
		end

		if (@comunaDespacho is null or len(@comunaDespacho) = 0)
		begin
			set @error = -1
			set @error_message = N'No existe comuna de despacho.'
			goto MensajeError
		end

		if (@ciudadFactura is null or len(@ciudadFactura) = 0)
		begin
			set @error = -1
			set @error_message = N'No existe ciudad de factura.'
			goto MensajeError
		end

		if (@ciudadDespacho is null or len(@ciudadDespacho) = 0)
		begin
			set @error = -1
			set @error_message = N'No existe ciudad de despacho.'
			goto MensajeError
		end
	END
end

--------------------------------------------------------------------------------------------------------------------------------

--------------------------------------------------------------------------------------------------------------------------------
/****************************** BLOQUEO LIMITE DE LINEAS EN FACTURA *****************************/
IF @object_type = '13' AND @transaction_type in ('A')
BEGIN

	declare @NumLinFact as int
	set @NumLinFact = (select count(B.LineNum) from OINV A INNER JOIN INV1 B ON B.DocEntry=A.DocEntry WHERE A.DocEntry = @list_of_cols_val_tab_del and A.Series = '37' AND A.DataSource <> 'O')

    if @NumLinFact > 30

	begin
    select @error = 1301
	select @error_message = 'FAE - Ha superado el limite de lineas del formato Factura, Max 30'
    end

END
/************************************************************************************************/
/****************************** BLOQUEO LIMITE DE LINEAS EN NC *****************************/
IF @object_type = '14' AND @transaction_type in ('A')
BEGIN

	declare @NumLinNC as int
	set @NumLinNC = (select count(B.LineNum) from ORIN A INNER JOIN RIN1 B ON B.DocEntry=A.DocEntry WHERE A.DocEntry = @list_of_cols_val_tab_del and A.Series = '40' AND A.DataSource <> 'O')

    if @NumLinNC > 30

	begin
    select @error = 1401
	select @error_message = 'FAE - Ha superado el limite de lineas del formato NC, Max 30'
    end

END
/************************************************************************************************/
/****************************** BLOQUEO LIMITE DE LINEAS EN FACTURA *****************************/
IF @object_type = '67' AND @transaction_type in ('A')
BEGIN

	declare @NumLinGD as int
	set @NumLinGD = (select count(B.LineNum) from OWTR A INNER JOIN WTR1 B ON B.DocEntry=A.DocEntry WHERE A.DocEntry = @list_of_cols_val_tab_del and A.Series = '41' AND A.DataSource <> 'O')

    if @NumLinGD > 30

	begin
    select @error = 6701
	select @error_message = 'FAE - Ha superado el limite de lineas del formato GD, Max 30'
    end

END
/************************************************************************************************/


--------------------------------------------------------------------------------------------------------------------------------

/**************************** VALIDA ULTIMO PRECIO DETERMINADO ****************************/
If @object_type = '59' and @transaction_type in ('A','U') --Identificar object_type y tipo de transacción--
begin	
	If (select T0.GroupNum from OIGN T0 where T0.DocEntry = @list_of_cols_val_tab_del) <> -2
	begin 
		select @error = 5901
		select @error_message = 'Debe seleccionar Lista de Precio "Ultimo Precio Determinado"'
	end
end
/******************************************************************************************/

/**************************** VALIDA IVA DIRECCION DESTINO CLIENTE ************************/
/*IF @object_type = 2 and @transaction_type in ('A','U')
begin
	If isnull((select T1.TaxCode from OCRD T0
		inner join CRD1 T1 on T0.CardCode = T1.CardCode
		where T0.CardType = 'C' and T0.CardCode=@list_of_cols_val_tab_del),'') <> 'IVA'
	begin
		select @error = 2
		select @error_message = 'Debe ingresar Direccion Destino e Indicador de Impuesto IVA'
	end
end*/
/******************************************************************************************/

--/*************** VALIDA ALMACEN DIFERENTE EN CABECERA DE STOCK TRANSFER *******************/

--If @object_type = 67 and @transaction_type in ('A','U') --Identificar object_type y tipo de transacción--
--begin	
--	If	(select T0.ToWhsCode from OWTR T0 where T0.DocEntry = @list_of_cols_val_tab_del) =
--		(select T0.Filler from OWTR T0 where T0.DocEntry = @list_of_cols_val_tab_del)
--	begin 
--		select @error = 1
--		select @error_message = 'Almacén de origen debe ser diferente de almacén destino'
--	end
--end

--/******************************************************************************************/


/**************************** U_AccType Paylink 1024 ************************/
If @object_type = '2' and @transaction_type in ('A','U')
begin
	If (select T0.CardType from OCRD T0 where T0.CardCode = @list_of_cols_val_tab_del) = 'S'
	begin
		If isnull((select T0.U_PL_AccType from OCRD T0 where T0.CardCode = @list_of_cols_val_tab_del),'') = 'CC'
		begin
			If isnull((select T0.BankCode from OCRD T0 where T0.CardCode = @list_of_cols_val_tab_del),'') = ''
			begin
				select @error = 2
				select @error_message = 'BES - Debe ingresar un numero de cuenta para el proveedor'
			end
		end
	end
end
/******************************************************************************************/

/**************************** VALIDA QUE LAS ENTRADAS INV LLEVEN COSTO ********************/
IF @object_type = '59' and @transaction_type in ('A','U')
BEGIN

	declare @Item as varchar(20)
	set @Item = (select top 1 B.ItemCode from OIGN A inner join IGN1 B on B.DocEntry=A.DocEntry where A.DocEntry = @list_of_cols_val_tab_del and isnull(B.Price,0) = 0)
		
	If @Item is not null
	begin 
		select @error = 5902
		select @error_message = 'El Articulo '+@Item+' no tiene costo ingresado en el documento'
	end
END
/******************************************************************************************/

/**************************** VALIDA QUE LAS ENTRADAS COMP LLEVEN COSTO *******************/
IF @object_type = '20' and @transaction_type in ('A','U')
BEGIN

	declare @Itemm as varchar(20)
	set @Itemm = (select top 1 B.ItemCode from OPDN A inner join PDN1 B on B.DocEntry=A.DocEntry where A.DocEntry = @list_of_cols_val_tab_del and isnull(B.Price,0) = 0)
		
	If @Itemm is not null
	begin 
		select @error = 2001
		select @error_message = 'El Articulo '+@Itemm+' no tiene costo ingresado en el documento'
	end
END
/******************************************************************************************/
/**************************** ER: Valida pedidos ecommerce duplicados  *******************/
if @transaction_type in ('A') and @object_type = 13 
begin
 
declare @numatcard as varchar(25)
 
set @numatcard = (select numatcard from OINV where DocEntry = @list_of_cols_val_tab_del and isIns='Y' )
IF @numatcard IS NOT NULL
	begin
	IF Exists (SELECT COUNT(numatcard) FROM OINV WHERE NumAtCard = @numatcard and isIns='Y' HAVING COUNT(NumAtCard) > '1')
	begin
			set @error =-1000
			set @error_message =' TI - Numero referencia/VTEX duplicado :' + @numatcard
		end
	end
end
/******************************************************************************************/
/**************************** VALIDA QUE LAS ENTRADAS INV LLEVEN CECO ********************/
IF @object_type = '59' and @transaction_type in ('A') 
BEGIN
    DECLARE @OcrCod as varchar(5)
	declare @origen as char(1)
	set @OcrCod = (SELECT max(CASE WHEN ISNULL(B.OcrCode,'') <> '' OR ISNULL(B.OcrCode2,'') <> '' OR ISNULL(B.OcrCode3,'') <> '' THEN 'true' ELSE 'false' END) AS Resultado FROM OIGN A  INNER JOIN IGN1 B ON B.DocEntry = @list_of_cols_val_tab_del )
	set @origen = (SELECT A.DataSource FROM OIGN A WHERE A.DocEntry = @list_of_cols_val_tab_del )
	if @OcrCod = 'false' and @origen <> 'S'
    begin 
		select @error = 5903
		select @error_message = 'Atencion : Falta centro de costos 1'
	end
END
/******************************************************************** ********************/
/**************************** VALIDA QUE LAS SALIDAS INV LLEVEN CECO ********************/
IF @object_type = '60' and @transaction_type in ('A')
BEGIN
	set @OcrCod = (SELECT max(CASE WHEN ISNULL(B.OcrCode,'') <> '' OR ISNULL(B.OcrCode2,'') <> '' OR ISNULL(B.OcrCode3,'') <> '' THEN 'true' ELSE 'false' END) AS Resultado FROM OIGN A  INNER JOIN IGN1 B ON B.DocEntry = @list_of_cols_val_tab_del )
	set @origen = (SELECT A.DataSource FROM OIGN A WHERE A.DocEntry = @list_of_cols_val_tab_del )
	if @OcrCod = 'false' and @origen <> 'S'
    begin 
		select @error = 5903
		select @error_message = 'Atencion : Falta centro de costos'
	end
END
/******************************************************************** ********************/



MensajeError:
-- Select the return values
select @error, @error_message

end
GO
