dnl
dnl Autoconf configuration file for romutils.
dnl

AC_PREREQ(2.61)

AC_INIT([romutils], [0.1], [no-define])
AC_ARG_PROGRAM()

dnl ----------
dnl Checks for '--with-ABCD' arguments.
dnl ----------

AC_MSG_CHECKING(target board is)
AC_ARG_WITH(targetboard,
	[AS_HELP_STRING([c], [specify board nickname.
		[default=pxs2-ref]])],
	targetboard=$withval, targetboard=proxstream2-vodka)
AC_MSG_RESULT($targetboard)

AC_MSG_CHECKING(target proc is)
AC_ARG_WITH(targetproc,
	[AS_HELP_STRING([--with-targetproc=TYPE], [select processor within TYPE as follows.
		pro4, pxs2, ld6b.
		[default=pro4]])],
	[targetproc=$withval], [with_targetproc=pro4])
AC_MSG_RESULT($targetproc)

AC_MSG_CHECKING(subfeature is) 
AC_ARG_WITH(subfeature,
    [AS_HELP_STRING([--with-subfeature], [specify subfeature.
        [default=]])],
    subfeature=$withval, subfeature=)
AC_MSG_RESULT($subfeature)

AC_MSG_CHECKING(armtf is)
AC_ARG_WITH(armtf,
	[AS_HELP_STRING([--with-armtf], [use ARM Trusted Firmware.
		[default=no]])],
	armtf=$withval, armtf=no)
if [ test x"$armtf" != x"no" ] ; then
	loaderimage=unph_bl.bin
	bootimage=fip.bin
else
	loaderimage=u-boot-spl.bin
	bootimage=u-boot.bin
fi
AC_MSG_RESULT($armtf)

AC_MSG_CHECKING(ROM product id is)
case "$targetboard" in
	ph1-ld20-global-v4)
		romproductname=EMMC_THGBMHG7C1LBAIL
		;;
	*)
		;;
esac
AC_MSG_RESULT($romproductname)

AC_MSG_CHECKING(usbupdatename is)
if [ test x"$subfeature" != x"" ] ; then
	usbupdatename=usb_update_$targetboard-$subfeature
else
	usbupdatename=usb_update_$targetboard
fi
AC_MSG_RESULT($usbupdatename)

if [ test -f ${srcdir}/$romproductname/partition_info.inc ] ; then
	source ${srcdir}/$romproductname/partition_info.inc
	uboot_partitions=""
	fsetup_sgdisk_command=""
	part_number=1
	part_start_addr=$part_top_addr
	for part_info in "${part[[@]]}"; do
		# part = (name mount_point filesystem file size bErase)
		data=(${part_info[[@]]})
		start_sect=`expr ${part_start_addr} / 512`
		size_sect=`expr ${data[[4]]} / 512`

		# make uboot_partitions (name=,start=,size=;)
		uboot_partitions=$uboot_partitions"name=${data[[0]]},start=`printf 0x%x ${part_start_addr}`,size=`printf 0x%x ${data[[4]]}`;"

		# make fsetup sgdisk command (-n number:start_sect:+size_sect -c number:"name")
		fsetup_sgdisk_command=${fsetup_sgdisk_command}"-n ${part_number}:${start_sect}:+${size_sect} -c ${part_number}:\"${data[[0]]}\" "

		# Check reserved area parameter
		case "${data[[0]]}" in
			bsp)
				bsp_start_sect_hex=`printf 0x%08x ${start_sect}`
				bsp_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			boot)
				boot_start_sect_hex=`printf 0x%08x ${start_sect}`
				boot_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			recovery)
				recovery_start_sect_hex=`printf 0x%08x ${start_sect}`
				recovery_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			system)
				system_start_sect_hex=`printf 0x%08x ${start_sect}`
				system_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			vendor)
				vendor_start_sect_hex=`printf 0x%08x ${start_sect}`
				vendor_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			userdata)
				userdata_start_sect_hex=`printf 0x%08x ${start_sect}`
				userdata_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			cache)
				cache_start_sect_hex=`printf 0x%08x ${start_sect}`
				cache_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			metadata)
				metadata_start_sect_hex=`printf 0x%08x ${start_sect}`
				metadata_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			vbmeta)
				vbmeta_start_sect_hex=`printf 0x%08x ${start_sect}`
				vbmeta_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			dtb)
				dtb_start_sect_hex=`printf 0x%08x ${start_sect}`
				dtb_size_sect_hex=`printf 0x%08x ${size_sect}`
				;;
			*)
				;;
		esac

		tmp=`expr ${part_start_addr} + ${data[[4]]}`
		part_start_addr=$tmp
		tmp=`expr ${part_number} + 1`
		part_number=${tmp}
	done
else
	AC_MSG_ERROR("Can't find ${srcdir}/$romproductname/partition_info.inc")
fi

AC_MSG_CHECKING(partition start address is)
AC_MSG_RESULT($part_top_addr)

AC_MSG_CHECKING(uboot_partitions is)
AC_MSG_RESULT($uboot_partitions)

AC_MSG_CHECKING(fsetup sgdisk parameter is)
AC_MSG_RESULT($fsetup_sgdisk_command)

if [ test -z ${bsp_start_sect_hex} ] ; then AC_MSG_ERROR("We need bsp partition"); fi
if [ test -z ${bsp_size_sect_hex} ]  ; then AC_MSG_ERROR("We need bsp partition"); fi

AC_SUBST(bsp_start_sect_hex)
AC_SUBST(bsp_size_sect_hex)

if [ test "${subfeature}" = "android" ] ; then
	if [ test -z ${boot_start_sect_hex} ] ; then AC_MSG_ERROR("We need boot partition"); fi
	if [ test -z ${boot_size_sect_hex} ]  ; then AC_MSG_ERROR("We need boot partition"); fi

	if [ test -z ${recovery_start_sect_hex} ] ; then AC_MSG_ERROR("We need recovery partition"); fi
	if [ test -z ${recovery_size_sect_hex} ]  ; then AC_MSG_ERROR("We need recovery partition"); fi

	if [ test -z ${system_start_sect_hex} ] ; then AC_MSG_ERROR("We need system partition"); fi
	if [ test -z ${system_size_sect_hex} ]  ; then AC_MSG_ERROR("We need system partition"); fi

	if [ test -z ${vendor_start_sect_hex} ] ; then AC_MSG_ERROR("We need vendor partition"); fi
	if [ test -z ${vendor_size_sect_hex} ]  ; then AC_MSG_ERROR("We need vendor partition"); fi

	if [ test -z ${userdata_start_sect_hex} ] ; then AC_MSG_ERROR("We need userdata partition"); fi
	if [ test -z ${userdata_size_sect_hex} ]  ; then AC_MSG_ERROR("We need userdata partition"); fi

	if [ test -z ${cache_start_sect_hex} ] ; then AC_MSG_ERROR("We need cache partition"); fi
	if [ test -z ${cache_size_sect_hex} ]  ; then AC_MSG_ERROR("We need cache partition"); fi

	if [ test -z ${metadata_start_sect_hex} ] ; then AC_MSG_ERROR("We need metadata partition"); fi
	if [ test -z ${metadata_size_sect_hex} ]  ; then AC_MSG_ERROR("We need metadata partition"); fi

	if [ test -z ${vbmeta_start_sect_hex} ] ; then AC_MSG_ERROR("We need vbmeta partition"); fi
	if [ test -z ${vbmeta_size_sect_hex} ]  ; then AC_MSG_ERROR("We need vbmeta partition"); fi

	if [ test -z ${dtb_start_sect_hex} ] ; then AC_MSG_ERROR("We need dtb partition"); fi
	if [ test -z ${dtb_size_sect_hex} ]  ; then AC_MSG_ERROR("We need dtb partition"); fi


	AC_SUBST(boot_start_sect_hex)
	AC_SUBST(boot_size_sect_hex)
	AC_SUBST(recovery_start_sect_hex)
	AC_SUBST(recovery_size_sect_hex)
	AC_SUBST(system_start_sect_hex)
	AC_SUBST(system_size_sect_hex)
	AC_SUBST(vendor_start_sect_hex)
	AC_SUBST(vendor_size_sect_hex)
	AC_SUBST(userdata_start_sect_hex)
	AC_SUBST(userdata_size_sect_hex)
	AC_SUBST(cache_start_sect_hex)
	AC_SUBST(cache_size_sect_hex)
	AC_SUBST(metadata_start_sect_hex)
	AC_SUBST(metadata_size_sect_hex)
	AC_SUBST(vbmeta_start_sect_hex)
	AC_SUBST(vbmeta_size_sect_hex)
	AC_SUBST(dtb_start_sect_hex)
	AC_SUBST(dtb_size_sect_hex)
fi

AC_SUBST(targetproc)
AC_SUBST(targetboard)
AC_SUBST(subfeature)
AC_SUBST(romproductname)
AC_SUBST(usbupdatename)
AC_SUBST(loaderimage)
AC_SUBST(bootimage)
AC_SUBST(uboot_partitions)
AC_SUBST(fsetup_sgdisk_command)

dnl ----------
dnl  Specify the Makefile
dnl ----------
AC_CONFIG_FILES([Makefile
                 $romproductname/Makefile
])
if [ test -f ${srcdir}/$romproductname/${usbupdatename}.txt.in ] ; then
AC_CONFIG_FILES([$romproductname/${usbupdatename}.txt])
fi

AC_OUTPUT()
